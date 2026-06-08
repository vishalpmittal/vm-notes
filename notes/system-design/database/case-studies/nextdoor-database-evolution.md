# Nextdoor's Database Evolution

A case study in disciplined database scaling — five stages from single Postgres to sharded multi-cluster, with each stage introducing new consistency challenges that required progressively sophisticated safeguards.

## Key Takeaways

- **Don't pick the most complex tool first.** Nextdoor walked a deliberate ladder: single Postgres → pooling → primary-replica → cache → CDC → sharding. Each step solved a real, current problem
- Every solution **introduced new consistency challenges** that needed their own fix — PgBouncer added complexity, replicas added lag, caches added staleness, CDC added pipeline ops
- **Time-based primary routing** is a pragmatic fix for read-your-writes — pin a user's reads to the primary briefly after they write, so they see their own changes
- **CDC as a reconciliation safety net** — Debezium tails the WAL and a background service invalidates stale cache entries. Eventual consistency without giving up the cache
- Sharding is the **last lever pulled, not the first** — it eliminates cross-shard joins and creates the most operational complexity. Defer until horizontal growth genuinely needs it

> "Great engineering is rarely about choosing the most complex tool first. It is about a disciplined progression."

## The Evolution

### Stage 1: Connection Pooling (PgBouncer)

**Problem:** Postgres uses one OS process per connection. With thousands of concurrent app workers, the box spent more time context-switching than running queries.

**Solution:** PgBouncer middleware. Thousands of workers → a few hundred warm DB connections.

**Why this is stage 1:** It addresses the immediate bottleneck without changing the data model or topology.

### Stage 2: Primary-Replica Architecture

**Problem:** Read-heavy traffic (hundreds of reads per write) competed with writes for primary CPU and IO.

**Solution:** Writes go to primary; reads go to multiple replicas.

**New problem:** Async replication lag — users could see stale data, including their *own* writes.

**Mitigation: Time-based dynamic routing.** After a user writes, route their reads to the primary for a window (seconds to minutes). Other users still hit replicas. Read-your-writes preserved without losing the read-scaling benefit.

See also [distributed-system-failure-modes § read-write splitting](../distributed-system-failure-modes.md#2-read-write-splitting) and [replication-and-sharding.md](replication-and-sharding.md).

### Stage 3: Valkey Caching Layer

**Problem:** Even with replicas, DB queries were expensive for hot data.

**Solution:** Valkey (Redis fork) in-memory cache, look-aside pattern, MessagePack for compact serialization.

**New problem:** Cache and DB diverge — race conditions during update.

**Mitigation:** Versioning engine using Postgres triggers + Lua atomic compare-and-set ops. Cache entries carry a version; writes use compare-and-set to prevent overwriting newer data.

See [caching-strategies.md](../caching-strategies.md) for the cache-aside / write-through pattern landscape.

### Stage 4: Change Data Capture (Debezium)

**Problem:** Even with versioning, network failures or app crashes could still leave the cache stale.

**Solution:** Debezium tails Postgres's WAL, streams change events to a background reconciler service that invalidates inconsistent cache entries.

The CDC layer is a **safety net** — not the primary cache-update path, but the catch-all for the cases the primary path misses.

See [cdc.md](cdc.md) for the broader CDC pattern.

### Stage 5: Database Sharding

**Problem:** Single primary, even highly optimized, can't handle billions of rows.

**Solution:** Shard tables across clusters by Neighborhood ID (a natural domain partition).

**Trade-off accepted:** Cross-shard joins eliminated. Application has to do more work — distributed reads, application-side joins, careful query planning. Sharding is the last lever pulled because it's the most expensive in ongoing complexity.

See [replication-and-sharding.md § Picking a Shard Key](replication-and-sharding.md#picking-a-shard-key).

## The Pattern: "Each Solution Brings Its Own Problem"

A clean illustration of why scaling is iterative:

| Stage | Solves | Introduces |
|---|---|---|
| Single Postgres | Nothing (baseline) | Single point of contention |
| PgBouncer | Connection overhead | Middleware ops surface |
| Replicas | Read scaling | Replication lag → read-your-writes |
| Cache + versioning | Hot-read cost | Cache/DB divergence |
| CDC reconciler | Edge-case staleness | Pipeline ops, schema-evolution discipline |
| Sharding | Single-node ceiling | Cross-shard query complexity |

Each new layer earns its keep on the previous bottleneck, but it doesn't make the previous problems disappear — it changes their shape.

## Lessons

1. **Scale incrementally.** Premature sharding is more painful than late sharding.
2. **Measure before you migrate.** Each stage was triggered by a specific, measurable bottleneck — not anticipation.
3. **Plan for the consistency tax.** Every new layer added a consistency problem; reserve engineering budget for the safeguards (versioning, reconciliation, monitoring).
4. **CDC is underrated as a safety net.** Not just for analytics — tailing the WAL gives you a backstop that catches what the primary write path misses.
5. **Pick shard keys that match query patterns.** Neighborhood ID worked for Nextdoor because queries naturally partition that way. The wrong shard key creates fanout queries that defeat the point.

## Related

- [Replication and sharding](replication-and-sharding.md) — the general patterns Nextdoor applied
- [CDC](cdc.md) — the technology behind stage 4
- [Caching strategies](../caching-strategies.md) — the look-aside pattern + stampede prevention
- [Redis](redis.md) — Valkey is a Redis fork
- [Distributed system failure modes](../distributed-system-failure-modes.md) — read-write splitting, dual-write
- [Scaling fundamentals](../scaling-fundamentals.md) — when to scale up vs out

---

**Source:** https://blog.bytebytego.com/p/nextdoors-database-evolution-a-scaling
**Date:** 2026-06-05
**Tags:** nextdoor, database, scaling, postgres, pgbouncer, replication, valkey, redis, cdc, debezium, sharding, case-study, evolution
