# Datadog's Data Replication Pipeline

How Datadog built a CDC-based replication architecture to move data out of Postgres into systems optimized for search workloads — and the automation/schema-discipline that lets it run across many teams without breaking.

## Key Takeaways

- The triggering pain: **p90 latency hit 7 seconds** when joining 82,000 metrics with 817,000 configurations in Postgres. Root cause: forcing a transactional DB to do search work it wasn't designed for
- The fix is **CDC over Kafka** — Debezium tails Postgres's WAL, streams changes into Kafka, downstream consumers (search systems, analytics, caches) project the data into their own shapes
- **Async replication is the deliberate choice** — sync would couple primary write latency to downstream system health. Brief replication lag is an acceptable price for decoupling
- **Schema evolution is the defensive layer** that keeps the pipeline from breaking: automated validation blocks risky changes at deploy time + Confluent Schema Registry enforces Avro backward compatibility
- **Temporal workflow orchestration** automates provisioning the 7+ pipeline components per use case — making the pattern repeatable instead of bespoke

## The Triggering Problem

Datadog's Metrics Summary page had to join:
- 82,000 metrics
- 817,000 configurations

In Postgres, the result was **p90 latency = 7 seconds**.

The root cause wasn't slow Postgres. It was **using a transactional database for a search workload.** Postgres is optimized for OLTP — many small ACID transactions. The metrics+configs join is more like a search problem, which is what dedicated search engines (Elasticsearch, OpenSearch, Postgres + pgvector) are built for.

Once you accept that, the architecture question becomes: how do you keep the search system in sync with the transactional source without rebuilding the application?

## Architecture: CDC → Kafka → Consumers

```
Postgres (source of truth, OLTP)
     ↓ Debezium tails the WAL
Kafka (durable change stream)
     ↓ many consumers
Search systems    Analytics    Caches    Other services
```

Three core components:

### 1. Change Data Capture (Debezium)
Reads Postgres's Write-Ahead Log and emits a change event for every insert/update/delete. **No application changes needed** — the source code keeps doing its normal Postgres writes, and CDC observes them in the WAL.

### 2. Asynchronous Replication
The primary database **confirms the write immediately**; replicas (and downstream consumers) catch up afterward.

**Why async over sync:**
- Sync would tie primary write latency to the slowest downstream system
- If a downstream consumer hangs, primary writes stall
- Brief replication lag is an acceptable trade for keeping primary write latency stable

### 3. Kafka as Durable Buffer
Kafka persists the change stream. If a consumer goes offline, it can resume from its last offset when it comes back. Producers (Debezium) don't block on slow consumers.

This is the standard CDC+Kafka pattern (see [cdc.md](cdc.md)) — Datadog's contribution is the operational discipline around it.

## Schema Evolution as a First-Class Concern

Pipelines like this break for one reason more than any other: **someone changed the source schema without thinking about downstream consumers.**

Two defensive layers:

### Layer 1: Automated Pre-Deploy Validation
Schema changes get checked **before** they ship — risky changes (dropping a column, renaming, narrowing a type) get blocked. The check happens in CI, not after the breakage.

### Layer 2: Confluent Schema Registry + Avro
Every message in Kafka is Avro-serialized with a registered schema. The registry enforces **backward compatibility** — a consumer reading with schema v1 can read messages written with schema v2 (as long as v2 only added optional fields).

The combination means schema evolution is a managed process, not an accident waiting to happen.

## Temporal for Pipeline Orchestration

A CDC pipeline isn't just "Debezium and Kafka." A typical Datadog pipeline has 7+ components per use case:

- Debezium connector
- Kafka topic(s)
- Schema registry entry
- Consumer service(s)
- Monitoring + alerting
- Replay + reconciliation tooling
- Access control / permissions

Setting these up manually per pipeline is bespoke ops work. Datadog automates the provisioning with **Temporal** (a workflow orchestration platform — long-running, durable workflows).

The result: spinning up a new replication pipeline is a templated workflow, not a slack thread of manual steps.

## Why This Works for Datadog Specifically

The article's honest framing: this architecture matches Datadog's actual constraints, not a universal best practice.

- **Workloads incompatible with primary databases** — metrics search is genuinely a different shape than OLTP
- **Multi-team data sharing** — many internal teams need read access to the same data
- **Operational scale** — many pipelines, repeated patterns, automation pays back

The same pattern is overkill for a small startup with one DB and one app. The complexity is justified by the breadth of consumers.

## Generalizable Patterns

For other teams considering CDC-based replication:

1. **CDC removes coupling from the application** — no dual-write, no manual sync code, no risk of the app forgetting to publish events
2. **Async is the default** — only go sync if the downstream system being slow is acceptable to your primary write path
3. **Kafka (or equivalent) is the buffer** — handles replay, lets consumers fail without blocking producers
4. **Schema discipline is non-negotiable** — without it, schema changes silently break consumers
5. **Provisioning is itself a system** — Temporal or similar pays back when you have many pipelines

## Related

- [CDC](cdc.md) — the underlying pattern (Datadog is a production case)
- [Replication and sharding](replication-and-sharding.md) — replication topology fundamentals (this is async, log-based replication)
- [Distributed system failure modes § dual-write](../distributed-system-failure-modes.md#1-the-dual-write-problem) — what CDC eliminates
- [Event-driven systems](../event-driven.md) — schema evolution as event contracts
- [Nextdoor database evolution](nextdoor-database-evolution.md) — another company's CDC adoption (as a cache reconciliation safety net)
- [Cloud-native data architecture](cloud-native-data-architecture.md) — broader pattern of storage/compute separation

---

**Source:** https://blog.bytebytego.com/p/how-datadog-redefined-data-replication
**Date:** 2026-06-05
**Tags:** datadog, data-replication, cdc, debezium, kafka, postgres, schema-registry, avro, temporal, async-replication, case-study
