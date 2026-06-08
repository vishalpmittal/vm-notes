# Refactoring Databases

Why database refactoring is fundamentally different from code refactoring — and the expand/contract pattern that makes schema evolution safe under multi-team, multi-app, live-traffic constraints.

## Key Takeaways

- Database refactoring is **harder than code refactoring** because schemas are shared — mobile apps, admin portals, background jobs, analytics pipelines, and BI tools all depend on the same tables. One change touches all of them
- The **expand/contract pattern** (three phases — expand, transition, contract) is the canonical safe way to evolve a schema without coordinated team deploys
- **Dual writes** keep old and new schemas in sync during transition — at the application layer (visible, controllable) or database layer (triggers, generated columns)
- **Backfilling large tables** must be batched — a single transaction over millions of rows locks the table and breaks production
- The **hardest part is organizational, not technical**: actually completing the contract phase. Without discipline, old columns linger forever as zombie schema artifacts

## Why Database Refactoring Is Different

Code refactoring affects one repo at a time. You can rename a function, update its callers in the same PR, deploy atomically.

Schema refactoring is the opposite:

| Consumers | Examples |
|---|---|
| **Primary app** | The "owning" service |
| **Mobile apps** | iOS, Android — each on its own release cycle |
| **Admin portals** | Internal tools, often by different teams |
| **Background jobs** | Crons, queue workers, batch processors |
| **Analytics / BI** | Looker, dashboards, ad-hoc queries |
| **External integrations** | Partners querying via APIs that pass through to the DB |

Renaming a column doesn't just break "the code." It breaks every consumer — many of which you don't own, can't deploy synchronously, and may not even know exist.

> "A simple schema change can become a production risk."

## The Expand/Contract Pattern

The three-phase approach that lets schemas evolve safely:

### Phase 1: Expand

**Add the new structure alongside the old.** Don't remove anything. The schema now has both:

```sql
-- Before
users(id, full_name)

-- After expand
users(id, full_name, first_name, last_name)
```

Old code keeps working. New code can use the new columns.

### Phase 2: Transition

**Keep both schemas in sync** as consumers migrate.

Two synchronization approaches:

| Approach | How | Best for |
|---|---|---|
| **Application-level dual writes** | App writes to both old and new columns in the same transaction | Single service owns writes; logic is visible in code |
| **Database-level mechanisms** | Triggers or generated columns auto-sync | Multi-client scenarios where you can't audit every writer |

Example (application-level):
```python
def update_user_name(user_id, first, last):
    db.execute("""
        UPDATE users
        SET first_name = %s, last_name = %s, full_name = %s
        WHERE id = %s
    """, (first, last, f"{first} {last}", user_id))
```

Example (database-level via generated column):
```sql
ALTER TABLE users
  ADD COLUMN full_name VARCHAR(255)
  GENERATED ALWAYS AS (first_name || ' ' || last_name) STORED;
```

Both work; pick based on who owns the writers.

**Critical invariant:** both schemas must stay consistent until every consumer has migrated.

### Phase 3: Contract

**Once all consumers have moved to the new schema**, drop the old columns:

```sql
ALTER TABLE users DROP COLUMN full_name;
```

This is where most refactors fail — the old column lingers indefinitely because no one is sure it's safe to drop.

## Backfilling Large Tables

The naive approach:
```sql
UPDATE users SET first_name = SUBSTR(full_name, 1, INSTR(full_name, ' ') - 1);
```

On a million-row table, this locks the entire table for the duration of the update. Production halts. Bad.

The batched approach:
```sql
-- Batch updates of 10k rows at a time
UPDATE users
SET first_name = SUBSTR(full_name, 1, INSTR(full_name, ' ') - 1)
WHERE id BETWEEN :start AND :start + 10000;
-- Wait, repeat
```

For very large tables:
- Use a backfill script that processes in chunks (10k-100k rows)
- Insert a small sleep between batches to let normal traffic flow
- Track progress so you can resume after failures
- Schedule for low-traffic windows
- For Postgres specifically: use `CREATE INDEX CONCURRENTLY` for new indexes (no lock)

## The Organizational Challenge

> "The hardest aspect isn't technical — it's ensuring teams complete the contract phase and actually remove deprecated columns rather than accumulating schema artifacts indefinitely."

Common failure mode:
1. Team A deploys expand phase (new column added)
2. Team A's service migrates to new column
3. Team A moves on
4. Teams B, C, D never migrate — they still read `full_name`
5. Old column remains forever
6. Five years later, three different "full_name" / "name" / "displayName" columns coexist

Discipline to prevent this:
- **Set explicit deprecation dates** when adding the new schema
- **Track consumer migration** — who's still reading the old column? Use DB logs or query analysis
- **Make old columns visibly deprecated** — comment with deprecation date, OR enforce read denial via permissions to force migration
- **Schedule the contract phase like a release** — it's a real deliverable, not "we'll get to it"
- **Periodically audit schema** — list columns with TODO/DEPRECATED comments older than 6 months

## Why You Need This Pattern

Without expand/contract, you have three bad options:

| Option | Failure mode |
|---|---|
| Big-bang rename | Coordinated deploy across N teams; any one team's failure breaks everything |
| Schema branching | Conway's-law nightmare — every team has its own schema dialect |
| Avoid renames | Schema accretes garbage names forever |

Expand/contract is the **only path** that scales across multi-team, live-traffic refactors.

## Related

- [Distributed system failure modes § schema evolution](../distributed-system-failure-modes.md#5-schema-evolution) — the broader schema-as-contract framing
- [Event-driven systems § schema evolution](../event-driven.md) — same pattern for events (events are contracts too)
- [Replication and sharding](replication-and-sharding.md) — refactoring schemas in sharded setups adds an extra dimension
- [Nextdoor database evolution](case-studies/nextdoor-database-evolution.md) — disciplined incremental DB evolution at scale
- [Reddit Kafka migration](../case-studies/reddit-kafka-migration.md) — adjacent pattern for stateful-system migration (different beast, similar discipline)

---

**Source:** https://newsletter.systemdesignclassroom.com/p/refactoring-databases-is-a-different-animal
**Date:** 2026-06-05
**Tags:** database, refactoring, schema-evolution, expand-contract, dual-write, backfill, migration, organizational-discipline
