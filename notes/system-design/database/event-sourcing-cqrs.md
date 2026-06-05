# Event Sourcing and CQRS

## Key Takeaways

- **Event Sourcing** stores the sequence of state-changing events as the source of truth; current state is derived by replaying events
- **CQRS** (Command Query Responsibility Segregation) separates the write model from the read model — they can use different schemas, stores, and consistency guarantees
- The two patterns are independent but frequently paired — event sourcing produces the event stream; CQRS projects it into purpose-built read models
- Best for: audit-heavy domains (finance, healthcare), systems with vastly different read/write shapes, multi-view data, or cases where "how did we get here?" matters as much as the current state
- Cost: significant complexity — schema evolution, querying current state requires replay or projections, eventual consistency between command and query sides

## Event Sourcing

### State-Storage vs. Event-Sourcing

**State-storage (traditional):**
```
accounts table:
  id=42, balance=100
```
The current value is stored directly. History is lost (or kept in audit logs as a side-channel).

**Event-sourcing:**
```
events table:
  AccountOpened(id=42, balance=0)
  Deposited(id=42, amount=50)
  Deposited(id=42, amount=60)
  Withdrew(id=42, amount=10)
```
Current balance (`100`) is derived by replaying events. The event log *is* the truth.

### Deriving Current State

```
def balance(account_id):
    state = 0
    for event in events_for(account_id):
        if event.type == "Deposited":
            state += event.amount
        elif event.type == "Withdrew":
            state -= event.amount
    return state
```

**Optimization — snapshots:** periodically save a snapshot (`account=42, balance=100, at_event=#9001`) so replay only needs events after the snapshot.

### When It's Worth It

| Strong fit | Bad fit |
|---|---|
| Banking, ledgers, accounting | Simple CRUD apps |
| Healthcare (audit required) | UI-state stores |
| Order/payment systems | High-write, low-value data (logs, metrics) |
| Domains where "why" is a recurring question | Anywhere current state is all you ever need |

### Hard Problems

- **Schema evolution** — events are immutable; you can't fix a bug by editing past events. Strategies: versioned events, upcasting (transform old events to new shape on read), or new event types
- **Querying** — "users with balance > $100" needs replay or a projection (read model). You don't naturally have a `WHERE` clause
- **GDPR / right-to-be-forgotten** — immutable log conflicts with delete requirements. Crypto-shredding (encrypt PII per-user, throw away the key) is the usual answer

## CQRS

```
                ┌──────────────────┐
                │  Write model     │   normalized, transactional
   commands ──> │  (Postgres)      │
                └────────┬─────────┘
                         │ events / CDC / outbox
                         ↓
                ┌──────────────────┐
                │  Read model(s)   │   denormalized, query-optimized
   queries ───> │  (Elasticsearch, │
                │   Redis, etc.)   │
                └──────────────────┘
```

### Why Split

Traditional architectures use one model for both reads and writes — fine for CRUD, painful when the shapes diverge:

- **Reads** want denormalized, indexed-for-display, fast — Elasticsearch / Redis / materialized views
- **Writes** want normalized, transactional, validated — Postgres with foreign keys and constraints

CQRS lets each side optimize independently. The write model emits events (often via [transactional outbox](../event-driven.md)); read models subscribe and project into whatever shape serves their queries.

### When CQRS Pays Off

- Read/write ratios are extreme (10,000:1 reads to writes)
- Multiple read views needed (search by name, search by category, recent orders, recommendations) — each as its own projection
- Read and write have different consistency needs (writes need strong, reads can tolerate stale)
- Different teams own different read views

### When It Doesn't

- Simple CRUD — one model is fine, CQRS is overkill
- Strong read-after-write consistency required everywhere
- Small team — the operational burden of multiple stores is real

## Event Sourcing + CQRS Together

The natural pairing:

```
   commands ──> Write side
                  │
                  ▼
              event log (the source of truth)
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
     read       read       read
     model A    model B    model C
   (recent)   (analytics) (search)
```

Each read model is a *projection* — a fold over the event stream into a shape optimized for one query pattern. New requirement = new projection, rebuilt by replaying the event log.

## Trade-offs Summary

| Benefit | Cost |
|---|---|
| Complete audit trail (event sourcing) | Querying requires projections |
| Read/write tuned independently (CQRS) | Multiple stores to operate |
| Time-travel debugging — replay history | Schema evolution is hard |
| New views without write-side changes | Eventual consistency between command and query |
| Natural fit for event-driven architectures | Steep learning curve |

## Related

- [Event-driven systems](../event-driven.md) — outbox pattern for emitting events reliably
- [CDC](cdc.md) — alternative way to publish state changes when not using event sourcing
- [CAP](../cap.md) — eventual consistency on the read side

---

**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Date:** 2026-06-04
**Tags:** event-sourcing, cqrs, database, ddd, projections, audit, eventual-consistency, system-design
