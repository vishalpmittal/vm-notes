# Reddit's Petabyte-Scale Kafka Migration

How Reddit moved 500+ Kafka brokers managing 1+ petabyte of live data (tens of millions of messages/sec) from EC2 to Kubernetes — with zero client modifications, zero downtime, and reversibility at every step.

## Key Takeaways

- **500+ brokers, 1+ PB live data, tens of millions of msgs/sec** migrated from EC2 (Terraform + Puppet + custom scripts) to Kubernetes (Strimzi operator). Zero client modifications, zero downtime
- **Abstraction layers enable safe transitions.** A DNS facade between clients and brokers decoupled the migration from anyone calling the cluster
- **Six-phase incremental migration** — DNS facade → broker ID reorganization → mixed cluster → gradual partition migration → control plane (ZK→KRaft) → cleanup. Reversible at every step
- The team **temporarily forked Strimzi** to run mixed Kubernetes+EC2 brokers. Operational stability trumped design cleanliness — you can clean up the fork after the migration completes
- General lesson: **protect the logical state** (data, partition assignments); **treat physical infrastructure as replaceable**

## Why This Is Hard

Kafka is a stateful distributed system carrying production traffic 24/7. You can't:
- Stop the cluster to migrate
- Modify millions of producer/consumer clients
- Tolerate data loss on a petabyte of live messages
- Run a parallel cluster and dual-write (expensive, complex, error-prone for state-heavy systems)

The constraint: **migrate the running system while it serves real traffic.**

## The Six-Phase Strategy

### Phase 1: DNS Facade

The first move had nothing to do with Kubernetes. The team introduced a **controllable DNS naming layer** between clients and brokers.

Before: client → DNS → broker (direct address)
After: client → DNS → facade → broker

This decoupled clients from the underlying topology. Any future change to broker IPs/hostnames could happen behind the facade without client coordination.

**Lesson:** the cheapest migration move is one that buys you control. Add the abstraction layer first, before you need it.

### Phase 2: Broker ID Reorganization

Doubled the cluster size by adding new brokers with higher IDs alongside the existing low-ID brokers. Then decommissioned the low-ID brokers.

Why: this leaves clean ID ranges for the Kubernetes brokers (added next) and avoids ID collisions.

### Phase 3: Mixed Cluster — Kubernetes + EC2 Brokers Coexist

The team **forked Strimzi** (the Kubernetes Kafka operator) to support a mixed cluster. Kubernetes-managed brokers and EC2-managed brokers ran in the same cluster simultaneously.

This is the riskiest phase architecturally — two control planes for the same logical cluster. The fork was a deliberate trade: short-term operational complexity for long-term incremental migration safety.

### Phase 4: Gradual Partition Migration (Cruise Control)

With both broker types in the cluster, **Cruise Control** incrementally shifted partition leadership and data from EC2 brokers to Kubernetes brokers — over ~1 week.

This is the actual data movement. By spreading it across days:
- Each migration step is reversible (Cruise Control can move partitions back)
- Traffic patterns stay normal — no big-bang cutover
- Issues surface incrementally where they can be diagnosed

### Phase 5: Control Plane Migration (ZooKeeper → KRaft)

Once the data plane was on Kubernetes, the team migrated the control plane from ZooKeeper to KRaft (Kafka's built-in Raft-based metadata).

Order matters: do the data-plane migration first while ZooKeeper is stable; then do the control-plane migration on a stable data plane. Doing both simultaneously would have entangled their failure modes.

### Phase 6: Cleanup

- Remove custom configurations
- Switch from the forked Strimzi back to upstream Strimzi
- Decommission EC2 broker tooling

The cleanup is the easy phase — by this point, the heavy lifting is done.

## The Five Generalizable Lessons

### 1. Abstraction Layers Enable Safe Transitions
The DNS facade was the cheapest, highest-leverage move. Add the indirection layer **before** you need it, so you can change what's behind it without client coordination.

This applies broadly: service discovery, API gateways, feature flags, message brokers — all are indirection layers that buy you the ability to change implementations without changing callers.

### 2. Protect the Logical State; Treat Physical Infrastructure as Replaceable
The data, partition assignments, and topic configs are precious. The brokers themselves (EC2 instances vs Kubernetes pods) are commodity. Migration succeeds by preserving the former while swapping the latter.

### 3. Design Each Step for Reversibility
Every phase had a rollback path:
- DNS facade can route back to direct
- Cruise Control can reverse partition moves
- Mixed cluster lets you remove either broker type
- KRaft migration has documented downgrade paths

If you can't reverse a step, you can't do it safely on a petabyte of live data.

### 4. Operational Stability Trumps Design Cleanliness
The fork of Strimzi to support mixed clusters is **not** a beautiful design choice. It's tactical debt. But the alternative — wait for upstream Strimzi to support this, or do a big-bang cutover — was worse for the actual goal (a safe migration). Clean up the fork after.

### 5. Move the Data Plane Before the Control Plane
Migrating two stateful components simultaneously entangles their failure modes. Sequencing matters.

## Why "Tens of Millions of Messages Per Second" Makes This Especially Hard

At this scale:
- A 1% error rate = tens of thousands of failures per second
- A 10-second hiccup = hundreds of millions of messages backed up
- Cluster operations that take "a few minutes" instead take much longer due to data volume
- Clients can't tolerate connection blips — they need to keep producing/consuming through the migration

The six-phase, reversible-at-each-step approach is what makes this kind of scale survivable.

## Related

- [Replication and sharding](database/replication-and-sharding.md) — Kafka brokers are a sharded distributed log; same patterns
- [CDC § Datadog data replication](database/datadog-data-replication.md) — adjacent Kafka-based architecture
- [Event-driven systems](event-driven.md) — what Kafka serves
- [Service discovery](service-discovery.md) — the DNS facade is the canonical service discovery pattern
- [Distributed system failure modes](distributed-system-failure-modes.md) — the failures the reversibility protects against
- [Refactoring databases (case study)](database/refactoring-databases.md) — another stateful-system migration pattern

---

**Source:** https://blog.bytebytego.com/p/how-reddit-migrated-petabyte-scale
**Date:** 2026-06-05
**Tags:** reddit, kafka, kubernetes, migration, strimzi, cruise-control, kraft, zookeeper, dns-facade, stateful-systems, case-study
