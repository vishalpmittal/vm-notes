# Sync vs Async Communication

The fundamental distinction is "**do you wait?**" Sync makes the caller block until a response; async lets the caller continue. The skill is knowing what truly needs an immediate answer.

## Key Takeaways

- **Sync at the edge, async behind the edge.** Mature systems confirm user actions synchronously (immediate certainty) and process side effects asynchronously (responsiveness + resilience)
- **Sync** when correctness > throughput — login, payment authorization, inventory validation. Failure must surface immediately
- **Async** when work can complete later — notifications, analytics, downstream updates, fan-out. Trades immediate certainty for resilience and parallelism
- Async **eliminates blocking, but introduces ordering problems** — messages can arrive out of sequence, and silent inconsistencies (lock-door processed before door-opens) don't trigger alerts
- The fix: **design grouping keys around stateful entities** (device, user, order) — sequential within boundaries, parallel across them. Beware **hot partitions** where one key gets all the traffic

## Sync vs Async — Choosing

| | Sync | Async |
|---|---|---|
| **Wait for response?** | Yes | No |
| **Best when** | Cannot proceed without an answer | Work can complete later |
| **Examples** | Login check, payment auth, inventory validation, critical reads | Notifications, analytics, downstream updates, fan-out work |
| **Optimizes for** | Correctness, immediate feedback | Throughput, resilience, decoupling |
| **Failure mode** | Surfaces immediately | Surfaces eventually (or silently) |
| **Latency stacks?** | Yes — every hop adds to user-visible latency | No — user doesn't wait |
| **Coupling** | Temporal (both sides must be up at the same time) | Decoupled in time and space |

The binary framing is wrong. Real systems mix both deliberately.

## The "Edge Pattern"

```
[User action]
     ↓ SYNC — confirm immediately
[Edge service]                   ← user gets the OK here
     ↓ ASYNC — queue side effects
[Notification worker]
[Analytics worker]
[Inventory sync]
[Recommendation update]
```

The user gets immediate confirmation that their action was accepted. Behind the scenes, work continues asynchronously without making the user wait.

This pattern is the default for most modern web apps — it's what lets a checkout feel snappy while the email, fraud check, inventory update, loyalty points, and analytics all run on their own schedules.

## What Async Costs You

Going async eliminates blocking *and* introduces problems that didn't exist in sync code.

### 1. Lost Execution Order

Parallel processing + retries + variable network delays mean messages arrive out of sequence.

**Concrete failure:** IoT door lock receives `lock_door` after `door_open`. Both messages succeed. Each component worked. The door is now unlocked when it should be locked.

> "Nothing failed. No alerts fired. The system behaved exactly as designed."

This is the worst class of failure — silent, no alarm, only discoverable through downstream symptoms.

### 2. Hot Partitions

The fix for ordering is **grouping keys** — process all messages for one entity (device, user, order) on the same partition, sequentially.

But: if one entity gets 80% of the traffic, that partition becomes a bottleneck. Now you're back to sequential processing for the busy case, having paid the complexity of async architecture for none of the throughput benefit.

Mitigation:
- Choose grouping keys that distribute evenly
- Monitor per-partition lag, not just aggregate
- Plan for re-keying / re-partitioning when distribution shifts

### 3. Silent Inconsistencies

Sync systems make failures loud — a timeout is visible. Async systems can produce wrong results without anything appearing to fail. Defenses:

- **Idempotency keys** so retries don't double-apply
- **Outbox pattern** ([event-driven.md § dual-write](event-driven.md)) so writes and events stay consistent
- **End-to-end traces** with correlation IDs
- **Reconciliation jobs** that compare actual state to expected state
- **Alarms on derived metrics** (revenue, inventory, user count) — they catch divergence even when individual components look fine

## Decision Heuristic

Before making a call async, ask:

1. **Does the user need to know the result right now?** Yes → sync.
2. **Will the system get into a bad state if order is wrong?** Yes → grouping key required (or stay sync).
3. **Is the receiver always available?** No → async with retry + DLQ.
4. **Can the receiver process out-of-order?** No → grouping key or sync.

If you can't answer all four confidently, default to sync. Adding async later when you actually need throughput is easier than debugging a silent inconsistency in production.

## Related

- [Event-driven systems](event-driven.md) — async at architectural scale: idempotency, outbox, schema evolution
- [Distributed system failure modes § queue depth](distributed-system-failure-modes.md) — operational signals for async systems
- [API retries](api/retries.md) — idempotency, backoff, retry budgets
- [Real-time communication](api/real-time-communication.md) — polling vs SSE vs WebSockets for client-side async
- [Data processing patterns](data-processing-patterns.md) — async at batch / stream level

---

**Source:** https://blog.levelupcoding.com/p/sync-vs-async-communication
**Source:** https://newsletter.systemdesignclassroom.com/p/async-systems-scale-your-system-and-your-problems
**Date:** 2026-06-05
**Tags:** sync, async, communication, blocking, queues, ordering, grouping-keys, hot-partitions, idempotency, system-design
