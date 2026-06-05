# Distributed Transactions

## Key Takeaways

- **2PC (Two-Phase Commit)** gives ACID across nodes by blocking — coordinator polls participants, then commits if all agree; safe but slow and fragile (coordinator failure leaves participants blocked)
- **Saga** trades atomicity for liveness — a sequence of local transactions, each paired with a compensating action that undoes it if a later step fails
- **Vector Clocks** track causality between concurrent updates in eventually-consistent systems — they can detect conflicts but not resolve them
- Modern microservice architectures prefer **Saga over 2PC** — blocking distributed locks don't scale; eventual consistency with compensation does
- Choosing: 2PC for small clusters where blocking is acceptable (e.g., 2-3 DBs in the same datacenter); Saga for cross-service business workflows; Vector Clocks for AP datastores like Dynamo/Cassandra

## Two-Phase Commit (2PC)

A coordinator orchestrates a transaction across multiple participants (e.g., two databases) to either all commit or all abort.

### Phase 1 — Prepare
```
Coordinator → Participant 1: "Can you commit?"
Coordinator → Participant 2: "Can you commit?"
Participants reserve resources, write to WAL, reply YES or NO
```

### Phase 2 — Commit (if all said YES)
```
Coordinator → Participant 1: "Commit"
Coordinator → Participant 2: "Commit"
Participants apply changes durably, ack
```

If any participant replied NO in Phase 1, the coordinator sends `Abort` instead.

### Failure Modes

| Failure | Outcome |
|---|---|
| Participant crashes during prepare | Coordinator aborts |
| Coordinator crashes after prepare, before commit | **Participants block, holding locks, waiting for the coordinator to come back** |
| Network partition between phases | Participants in `prepared` state are stuck |

The **blocking** property is the killer for high-scale systems. Locks held across the network mean any slow participant degrades the whole transaction.

### When 2PC Is OK

- Small number of participants (2-3)
- Same datacenter, low latency
- Resources can tolerate brief locking
- You actually need ACID across nodes (e.g., distributed RDBMS, XA transactions across two DBs)

### When It's Not

- Microservices over WAN
- Long-running workflows (minutes/hours)
- High write throughput
- Heterogeneous systems (some don't support XA)

## Saga Pattern

Replace one big distributed transaction with a sequence of local transactions, each with a defined **compensating action** to undo it.

### Example: E-commerce Order

```
1. ReserveInventory      compensate: ReleaseInventory
2. ChargePayment         compensate: RefundPayment
3. CreateShipment        compensate: CancelShipment
4. SendConfirmation      compensate: SendCancellation
```

If step 3 fails, run the compensating actions for steps 2 and 1 in reverse. The system reaches a consistent state, just not the originally intended one.

### Two Orchestration Styles

| Style | How | Pros | Cons |
|---|---|---|---|
| **Choreography** | Each service listens for events and emits new events; no central coordinator | Loosely coupled; no SPOF | Hard to see the full workflow; cyclical event dependencies emerge |
| **Orchestration** | A central orchestrator service drives the saga step by step | Explicit, observable, testable | Orchestrator is a new component to operate |

For complex sagas (>4 steps, conditional branching), orchestration usually wins. For simple ones, choreography is lighter.

### Hard Parts

- **Compensation isn't always possible** — you can't un-send an email or un-publish a tweet. Design around it: hold sends until after the saga commits
- **Isolation** — sagas aren't isolated; intermediate states are visible to other transactions. Use "semantic locks" (a flag like `payment_status=PENDING`) to prevent concurrent operations on the same entity
- **Idempotency** — every step and every compensation must be idempotent because retries are guaranteed
- **Observability** — without good tracing, debugging a half-failed saga is brutal

## Vector Clocks

For systems that accept concurrent writes on multiple nodes (Dynamo, Riak, Cassandra LWT alternatives), wall-clock timestamps don't work — clocks drift and skew. **Vector clocks** provide a partial ordering of events that captures causality.

### Mechanics

Each node maintains a counter; each event carries the vector of counters from every node it's seen.

```
Initial: VC = {A:0, B:0, C:0}

Node A writes:       VC = {A:1, B:0, C:0}
Node B reads A's,
  then writes:       VC = {A:1, B:1, C:0}
Node C writes
  independently:     VC = {A:0, B:0, C:1}
```

Compare two vectors:
- All components ≥ → one happens-before the other (causal)
- Mixed (some ≥, some <) → **concurrent / conflicting writes**

### What Vector Clocks Do and Don't

| Do | Don't |
|---|---|
| Detect concurrent writes | Resolve the conflict |
| Track causality across nodes | Scale to thousands of nodes (vector grows) |
| Work without synchronized clocks | Replace business-level conflict resolution |

Conflict resolution is the application's job: last-writer-wins (lossy), merge (CRDTs, sets), or surface to user (Dropbox-style "you have a conflict").

### Practical Use

- **Riak** — vector clocks (now "dotted version vectors") detect concurrent writes; siblings are returned for app-level resolution
- **Cassandra** — chose simpler timestamp-based LWW; faster but lossier
- **CRDTs** — encode the merge function into the data type so conflicts resolve automatically

## Picking the Right Tool

```
Need ACID across 2-3 DBs in one datacenter?
  → 2PC / XA

Business workflow spanning multiple services?
  → Saga (orchestrated for complex flows, choreographed for simple)

Eventually consistent datastore with multi-node writes?
  → Vector Clocks (or CRDTs if you can model the data that way)
```

## Related

- [CAP](cap.md) — quorum reads/writes for tunable consistency
- [Event-driven systems](event-driven.md) — sagas are typically built on event buses; outbox for reliable step transitions
- [Distributed system failure modes](distributed-system-failure-modes.md) — partial failures, idempotency, retries underpin both 2PC participants and saga steps

---

**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Date:** 2026-06-04
**Tags:** distributed-transactions, two-phase-commit, 2pc, saga, vector-clocks, consistency, eventual-consistency, compensation, system-design
