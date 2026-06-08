# Scaling Fundamentals

## Key Takeaways

- **Horizontal scaling** (more machines) is the default for stateless services — it has no ceiling and survives single-node failures, but requires a load balancer and stateless design
- **Vertical scaling** (bigger machine) is the right answer for databases and single-threaded workloads — simpler to reason about, but has a finite ceiling and is a SPOF
- **Auto-scaling** turns capacity into a metric-driven control loop — pick the right signal (CPU, queue depth, p95 latency), tune the cooldowns, expect minutes of lag
- **Connection pooling** is non-optional for any DB-connected app — opening a TCP+TLS connection per request burns 10-100ms; pools amortize that cost
- Scaling rarely fixes a broken design — a slow query, a hot key, or a missing index won't be solved by adding nodes

## Horizontal vs. Vertical Scaling

|  | Horizontal (scale-out) | Vertical (scale-up) |
|---|---|---|
| **How** | Add more machines | Bigger CPU, more RAM, faster disk |
| **Ceiling** | Effectively infinite | Finite (hardware limits) |
| **Failure** | Survives single-node loss | Single point of failure |
| **Cost** | Linear-ish; cloud-friendly | Non-linear at the high end (bare-metal premiums) |
| **Best for** | Stateless services, web tiers, queues | RDBMS primaries, caches, single-threaded workloads |
| **Requires** | Load balancer, statelessness, distributed coordination | Nothing special — just provision a bigger box |

### Statelessness Is the Prerequisite

Horizontal scaling assumes any node can serve any request. If your service stores session state in local memory, every retry might hit a different node and miss the state. Push state out:

- **Session state** → Redis, signed cookies, JWT
- **File uploads** → object store (S3), not local disk
- **Caches** → external (Redis, Memcached), not in-process

### When to Scale Up Instead

Some workloads don't shard cleanly:
- **OLTP primaries** — partitioning is painful; scaling up postpones the day you have to shard
- **Single-writer queues** (within a partition) — can't horizontally scale the leader
- **Workloads with chatty cross-node coordination** — adding nodes adds latency

A common pragma: **scale up the database, scale out the app tier**.

## Auto-Scaling

```
   metric (CPU, latency, queue depth)
            │
            ▼
       threshold breached?
            │ yes
            ▼
      add/remove instance ──> wait for cooldown ──> back to metric
```

### Pick the Right Signal

| Signal | Good for | Bad for |
|---|---|---|
| **CPU utilization** | CPU-bound services | IO-bound services (always low CPU, queue grows) |
| **Request rate** | Predictable per-request cost | Variable request cost |
| **Queue depth / oldest message age** | Consumer scaling | Web tier scaling |
| **p95/p99 latency** | User-facing services | Has lag — latency rises *after* you're saturated |
| **Custom business metrics** | Anything else | Requires more pipeline work |

### The Lag Problem

Auto-scaling reacts *after* the metric trips. End-to-end:

```
spike → metric scrape (10s) → threshold (1 min) → launch instance (1-3 min)
       → instance ready (image pull, warmup, 1-2 min) → traffic flowing
```

That's **3-6 minutes** from spike to relief. For sudden bursts, pre-warm capacity or use predictive scaling.

### Common Mistakes

- **No cooldown** — flapping: add → metric drops → remove → metric spikes → add. Set a cooldown of ≥3-5 min
- **Scale-out only** — saving money on scale-in matters too; otherwise you pay for ghost capacity
- **Scaling on the wrong layer** — adding consumers when the DB is the bottleneck just amplifies DB pressure (see [distributed-system-failure-modes.md](distributed-system-failure-modes.md))

## Connection Pooling

Every request to a database without pooling does this:
```
TCP handshake (1 RTT) → TLS handshake (1-2 RTT) → auth (1 RTT) → query → close
```

At 10ms RTT, that's 40-50ms of overhead *before* the query runs. With pooling:

```
   ┌────────── pool ──────────┐
   │  conn1  conn2  conn3 ...  │
   └──┬─────────┬─────────┬────┘
      ▼         ▼         ▼
    request   request   request
```

Connections are kept warm and reused. Per-request overhead drops to sub-millisecond.

### Sizing the Pool

The classic mistake is making the pool too big. **A pool larger than the DB can handle just queues requests with extra overhead.**

Rule of thumb (Hikari, classic OLTP):
```
pool_size ≈ ((core_count × 2) + effective_spindle_count)
```
For an SSD-backed 8-core DB: ~16-20 connections **per app instance**. With 100 app instances, that's 1,600-2,000 total connections — likely over your DB's limit.

When pools × instances overflow the DB:
- Use a **PgBouncer / proxy pool** in front of the DB for connection multiplexing
- Use **per-tenant or per-workload pools** instead of one giant shared pool
- Use **serverless DB connection poolers** (RDS Proxy, Neon, Aurora Serverless)

### Pool Exhaustion Symptoms

- Requests time out waiting for a connection (not waiting for the query)
- Latency spikes correlated with `pool.getConnection()` not with query execution
- DB shows low CPU but app shows queued requests

Fix root cause first (slow queries, long transactions holding connections), tune pool size last.

## When Scaling Won't Save You

Things scaling does *not* fix:

- A query that does a full table scan
- A hot key (one user/tenant generating 80% of traffic)
- A missing index
- A synchronous cross-service call chain (latency stacks regardless of replicas)
- N+1 queries
- An external API rate limit

Fix the design, then scale.

## Related

- [Load balancer](load-balancer.md) — the prerequisite for horizontal scaling
- [Distributed system failure modes](distributed-system-failure-modes.md) — amplification loop, cascading failures (scaling-related antipatterns)
- [Caching strategies](caching-strategies.md) — reduce DB load instead of scaling the DB

---

## Additional Connection Pool Parameters

Beyond the basic sizing covered above, production pools have four key knobs:

| Parameter | What | Trade-off if wrong |
|---|---|---|
| **Min / Max size** | Idle connections kept ready vs ceiling under load | Too low → connection thrashing; too high → DB resource waste |
| **Idle timeout** | How long unused connections persist | Too short → reconnect overhead; too long → orphaned connections during DB restarts |
| **Wait timeout** | Max time a request queues before failing | Too short → user-visible failures on bursts; too long → backed-up requests pile up |
| **Validation strategy** | When to test connection health (borrow / release / periodic) | Too aggressive → latency cost; too lax → broken connections served to app |

**The diagnostic warning:** a healthy pool can mask deeper problems. If queries are CPU- or I/O-bound on the DB, no pool tuning fixes that — the pool just becomes the new bottleneck visibility layer. Fix the slow query before adding more connections.

---

**Source:** https://designgurus.substack.com/p/50-system-design-patterns-every-engineer
**Source:** https://blog.levelupcoding.com/p/connection-pooling-clearly-explained
**Date:** 2026-06-04 (initial), 2026-06-05 (added connection-pool parameter detail)
**Tags:** scaling, horizontal-scaling, vertical-scaling, auto-scaling, connection-pooling, capacity-planning, system-design
