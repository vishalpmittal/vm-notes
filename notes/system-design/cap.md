# Consistency Models in Distributed Systems

## Key Takeaways

- Consistency is not binary -- it is a spectrum from linearizable (strongest) to eventual (weakest), and the right choice depends on what breaks if a read is stale
- Quorum-based consistency (R + W > N) provides a tunable dial rather than a fixed guarantee, letting you mix models per workflow
- Isolate strong consistency to the critical path (payments, inventory) and let derived data (analytics, search indexes, recommendations) converge asynchronously
- Three common mistakes: over-provisioning consistency everywhere, under-designing for staleness, and treating consistency as a platform-wide property instead of a per-workflow decision

## Why Consistency Gets Expensive

In a single-node system, every read hits the same node -- no coordination needed. Once you replicate data across multiple nodes, coordination is required to keep replicas in sync, and that coordination costs latency.

![Single-node vs multi-node replication cost](../images/20260531-1010-consistency-replication-cost.png)

During the replication lag window, reads from stale replicas return outdated values. The fundamental question becomes: how much staleness can each workflow tolerate?

## Four Levels of Consistency

![Four levels of consistency comparison](../images/20260531-1010-consistency-models-overview.png)

### Linearizable

The strongest guarantee. After a write completes, every subsequent read -- from any client, on any node -- must return that value or a newer one. The system behaves as if there is a single copy of the data.

- **Use when:** A stale read causes real damage -- financial ledgers, inventory counts, distributed locks
- **Cost:** High latency, low availability (requires coordination across all replicas before acknowledging)

### Causal

Preserves ordering for causally related operations while allowing unrelated events to proceed independently. If User A posts a message and User B replies, every observer sees the post before the reply -- but unrelated posts can appear in any order.

- **Use when:** Context and ordering matter but global real-time ordering is overkill -- social feeds, messaging, collaborative editing
- **Cost:** Moderate latency; tracks causal dependencies (vector clocks, version vectors)

### Eventual

Replicas may diverge temporarily but will converge to the same value given enough time. No ordering guarantees during the convergence window.

- **Use when:** Brief staleness is harmless -- analytics dashboards, view counters, recommendation engines, caches
- **Cost:** Lowest latency, highest availability; must design for temporary inconsistency

### Quorum-Based (Tunable)

Rather than picking a fixed model, quorum systems let you tune consistency per operation by controlling how many replicas participate in reads (R) and writes (W) out of N total replicas.

![Quorum consistency as a dial](../images/20260531-1010-quorum-dial.png)

- **Strong consistency:** R + W > N (read and write sets must overlap, guaranteeing the read sees the latest write)
- **Eventual consistency:** R + W <= N (no guaranteed overlap, reads may return stale data)
- **Key insight:** R + W > N guarantees overlap but does not guarantee linearizability. You still need read-repair, version vectors, or consensus to close the gap.

## Mixed Consistency in Practice: Checkout Flow

Real systems don't use one consistency model everywhere. A checkout flow illustrates the split:

![Checkout flow with mixed consistency](../images/20260531-1010-checkout-mixed-consistency.png)

**Critical path (strong consistency, synchronous):**

1. Reserve inventory -- cannot oversell
2. Charge payment -- cannot double-charge
3. Create order record -- source of truth

**Side effects (eventual consistency, asynchronous):**

- Update analytics (can lag seconds/minutes)
- Send confirmation email (brief delay acceptable)
- Refresh search index (stale data is acceptable)
- Update recommendations (best-effort, no harm if delayed)

The rule: keep the critical path small and correct. Let everything else converge later.

## Consistency Decision Framework

Use these four questions to pick the right guarantee for each workflow:

![Consistency decision framework flowchart](../images/20260531-1010-consistency-decision-framework.png)

1. **If this read is stale, does something break?** Yes --> Linearizable. No --> continue.
2. **Does the order of related events matter?** Yes --> Causal. No --> continue.
3. **Can you tolerate seconds or minutes of staleness?** Yes --> Eventual. No --> continue.
4. **Can the strict guarantee be isolated to a smaller scope?** Yes --> Quorum/Mixed. No --> Linearizable on the full path.

The guiding principle: *"What is the weakest model that still protects us from unacceptable wrongness?"*

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| Over-provisioning consistency | Applying linearizable reads globally adds latency to every request | Isolate strong consistency to business-critical boundaries only |
| Under-designing for staleness | Assuming eventual consistency "just works" without handling conflicts | Define explicit staleness windows; implement conflict resolution |
| Treating consistency as platform-wide | One global setting instead of per-workflow tuning | Make consistency a per-workflow decision at the data layer |

## Consistency vs Accuracy

A useful distinction that gets conflated: **consistency** ≠ **accuracy**.

- **Consistency** (CAP sense) — replicas agree on the value
- **Accuracy** — the value reflects reality

These can diverge in both directions:

| Scenario | Consistent? | Accurate? |
|---|---|---|
| User updates email; primary shows new value, replicas show old | ❌ No | ✅ Yes (somewhere) |
| Bug writes incorrect tax rate; all nodes replicate the wrong value perfectly | ✅ Yes | ❌ No |

> "Consistency protects agreement about the order of writes, not the truth of the data itself."

The implication: **consistency protocols can't make your data correct**. Correctness depends on application validation and business logic. You can have a perfectly consistent system that's confidently wrong.

This matters for decision-making:
- For payments/banking, you need both (and you pay latency for it)
- For social feeds, you need neither very strongly (eventual consistency, eventual accuracy fine)
- For analytics, you usually want accuracy with weak consistency (correct numbers eventually)

> "Good system design isn't about perfection — it's about choosing which failure mode causes the least damage."

## Additional Framings

**CAP as a runtime choice, not a permanent classification.** A useful reframe: the C/A/P triangle isn't about what your system gives up forever — it's about *what happens when the network splits*. Most of the time partitions don't exist, and the trade-off doesn't bite. The classification ("CP system" vs "AP system") describes the choice the system makes during the partition window, not a property that affects every request.

The practical decision heuristic that follows: **"What hurts your product more — returning incorrect data, or returning no data at all?"** Banking apps choose "no data" (CP — refuse to serve stale balances). Social feeds choose "stale data" (AP — show something rather than fail). Most real systems pick per component, not globally.

## PACELC: The "Else" Extension

CAP only covers behavior during partitions (the "PC" part). **PACELC** extends it to the normal case: **if Partitioned, choose C or A; Else, choose Latency or Consistency.**

| Partition behavior | Else behavior | Examples |
|---|---|---|
| **PC/EC** | Consistent always | Postgres in synchronous-replication mode, traditional RDBMS |
| **PC/EL** | Consistent on partition, low-latency normally | MongoDB (default), HBase |
| **PA/EC** | Available on partition, consistent normally | (uncommon) |
| **PA/EL** | Available + low-latency, lossy on consistency | Cassandra, DynamoDB, Riak (most "AP" datastores) |

PACELC captures the truth that even when partitions aren't happening, you're still trading consistency for latency on every read/write. Most systems people call "AP" are really "PA/EL" — they prioritize availability *and* latency over consistency at all times, not only during partitions.

---

**Source:** https://newsletter.systemdesignclassroom.com/p/consistency-is-negotiable-but
**Source:** https://blog.levelupcoding.com/p/cap-theorem-explained
**Source:** https://newsletter.systemdesignclassroom.com/p/consistency-vs-accuracy-distributed-systems
**Date:** 2026-05-31 (initial), 2026-06-05 (added CAP-as-runtime-choice framing + PACELC + consistency-vs-accuracy distinction)
**Tags:** consistency, accuracy, distributed-systems, cap-theorem, pacelc, quorum, linearizability, eventual-consistency, system-design
