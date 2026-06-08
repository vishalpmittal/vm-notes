# Pinterest's Pacer Async Compute Platform

How Pinterest rebuilt its async job processing platform (Pinlater → Pacer) to fix lock contention, lack of workload isolation, and 50%+ wasted dequeue work. Now handles **billions of daily executions** with sub-millisecond dequeue latency.

## Key Takeaways

- **Pacer replaces Pinlater**, Pinterest's aging async job platform. Same problem space (process billions of jobs/day) but a redesigned architecture
- The triggering pains: **lock contention** (many threads from many Thrift servers hammering same DB partitions), **no workload isolation** (CPU-heavy image processing competing with lightweight notifications), and **50%+ wasted dequeue queries** returning empty results
- **Three components**: stateless Thrift service (submits) → broker layer (Helix-coordinated partition ownership, in-memory buffering) → Kubernetes worker pods (per-queue isolation)
- **Exclusive partition assignment** via Apache Helix eliminates contention — exactly one broker owns each partition, no lock fights
- **Adaptive sharding** allocates partitions proportional to traffic, not uniformly — eliminating the wasted-empty-query problem

## What Pinlater Had Wrong

Pinlater was a workable async platform at small scale but broke as Pinterest's volumes grew:

| Problem | Symptom |
|---|---|
| **Lock contention** | Many Thrift servers' threads polling the same DB partitions simultaneously; each step waits on lock acquisition |
| **No workload isolation** | Image processing (CPU-heavy) and notifications (network-bound, fast) shared resources; one starves the other |
| **50%+ empty dequeue queries** | Uniform polling across all shards regardless of actual job distribution → most polls return nothing |
| **No FIFO at scale** | Multi-partition queues lost ordering guarantees |
| **Cascading failures** | A bad job type could exhaust shared resources, taking down unrelated workloads |

## Pacer's Three-Layer Architecture

```
   Clients
      ↓ submit jobs
   Stateless Thrift Service
      ↓ (write to DB)
   ┌─────────────────────────────────────┐
   │ Dequeue Broker Layer                 │
   │   - Apache Helix coordinates ownership│
   │   - Each broker exclusively owns N    │
   │     partitions; no cross-broker locks │
   │   - In-memory job buffer per partition│
   │     (sub-millisecond dequeue latency) │
   └─────────────────────────────────────┘
      ↓ dispatch
   Kubernetes Worker Pods
      - Isolated per queue
      - Custom resource allocation per queue
      - Independent scaling
```

### 1. Stateless Thrift Service (Submission)

Receives submissions and writes jobs to the DB. **Stateless** because:
- Submission is the user-facing critical path — must scale horizontally
- No coordination needed at submit time; the work is in dequeue/dispatch

### 2. Dequeue Broker Layer (The Big Idea)

The original Pinlater had every thread reading every shard — lock fight. Pacer uses **Apache Helix** (cluster coordination) to ensure **exactly one broker owns each partition**.

Brokers:
- **Pull jobs from the DB for their assigned partitions only** (no cross-broker contention)
- **Buffer jobs in memory** in thread-safe queues
- **Serve dequeue requests in sub-millisecond time** from the buffer (no DB roundtrip)

Effect: dequeue latency collapses, lock contention disappears, DB queries drop because brokers prefetch in batches.

### 3. Kubernetes Worker Pods (Per-Queue Isolation)

Each queue has dedicated Kubernetes pods with custom resource allocation. Benefits:
- CPU-heavy image processing scales independently from lightweight notification jobs
- A bad job type's resource usage can't starve unrelated queues
- Per-queue SLOs become tractable

## Key Innovations

### Exclusive Partition Assignment (Helix)
Eliminates the contention model. Helix is Apache's cluster-management framework — it does the work of assigning partitions exclusively and rebalancing when brokers fail.

This is the **same insight as Kafka consumer groups** — partition ownership per consumer eliminates locking. Pinterest applied it to a job-queue context.

### In-Memory Caching Reduces Dequeue Latency
Brokers buffer jobs in thread-safe queues. Dequeue requests hit the in-memory queue rather than the DB. Latency: sub-millisecond.

### Adaptive Sharding
Pinlater spread queues uniformly across shards regardless of traffic. Pacer allocates partitions proportional to actual queue traffic. Low-volume queues get few partitions; hot queues get many. Wasted polling drops dramatically.

### Queue-Level Isolation
Custom resource allocation per queue means:
- No "noisy neighbor" between queues
- Independent scaling decisions
- Cascading failure containment

### FIFO via Single-Partition Queues
Queues that need strict ordering use single-partition assignment. Trade: lower throughput, ordering guarantees. The application picks per queue.

## Generalizable Patterns

The Pinlater → Pacer evolution has lessons for any team building queue-based systems:

1. **Partition ownership > polling.** Helix-style exclusive assignment beats lock-contended polling
2. **In-memory buffering between DB and workers** dramatically reduces dequeue latency
3. **Per-queue isolation** lets diverse workloads coexist without resource fights
4. **Adaptive sharding** beats uniform sharding when workload distribution is skewed (almost always)
5. **Stateless submission, stateful dispatch.** The pattern lets submission scale horizontally while dispatch coordination happens at the smaller broker layer
6. **Sub-millisecond dequeue from in-memory** is achievable and worth engineering for

## Comparison to Standard Queue Systems

| | RabbitMQ | Kafka | Pacer |
|---|---|---|---|
| Designed for | Generic messaging | Event streaming | Pinterest's async jobs |
| Ordering | Per-queue | Per-partition | Per-partition (single-partition for FIFO) |
| Throughput model | High via routing | Very high via partitioning | Very high via partition + buffer |
| Worker isolation | Limited | Limited | Per-queue K8s pods |
| Coordination | Erlang built-in | ZooKeeper / KRaft | Helix |
| Best for | Diverse message types | Event sourcing, streams | Batch async jobs at scale |

Pacer is closer to Kafka in coordination model (exclusive partition ownership) but explicitly optimized for the job-processing use case.

## Related

- [Event-driven systems](event-driven.md) — async at architectural scale
- [Sync vs async communication](sync-vs-async-communication.md) — when async is the right choice
- [Distributed system failure modes § queue depth](distributed-system-failure-modes.md#4-queue-depth) — the operational signals to watch
- [Service discovery](service-discovery.md) — Helix is in the same space as service discovery for cluster coordination
- [Scaling fundamentals](scaling-fundamentals.md) — per-workload isolation is a scaling pattern
- [Reddit Kafka migration](reddit-kafka-migration.md) — another stateful queue platform case study

---

**Source:** https://blog.bytebytego.com/p/how-pinterest-built-an-async-compute
**Date:** 2026-06-05
**Tags:** pinterest, pacer, pinlater, async-processing, job-queue, helix, kubernetes, partition-ownership, in-memory-buffer, adaptive-sharding, case-study
