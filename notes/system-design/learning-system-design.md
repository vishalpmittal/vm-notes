# Learning System Design

A methodology piece: engineers typically learn system design **backwards** — starting from tools and architecture diagrams instead of from the constraints that make systems work or fail.

## Key Takeaways

- **Start with constraints, not diagrams.** Memorizing architectural patterns without understanding the forces they address produces shallow fluency
- **Requirements first.** Ask clarifying questions before designing — read-heavy vs write-heavy, latency budget, data freshness — every "right answer" depends on these
- **Trade-offs are fundamental.** Speed conflicts with consistency; distributed coordination has latency cost; you don't get to optimize for everything
- **Behavior under pressure beats happy-path knowledge.** Real judgment comes from understanding failures, not from memorizing diagrams

> "Good system design starts with constraints, not diagrams."

## The Design Triad

**Requirements → Fundamentals → Architecture**

- **Requirements** — what matters (driven by use case, not technology)
- **Fundamentals** — why it matters (networks, storage, caching, queues, load balancing — the underlying forces)
- **Architecture** — what you choose as a result (the diagram is the output, not the input)

Skip the first two and you get cargo-cult architecture. Do them well and the architecture often falls out.

## Recommended Learning Sequence

1. **Networks** — communication costs and risks (latency, partitions, retries)
2. **Databases** — storage and access patterns (OLTP vs OLAP, indexing, replication)
3. **Caching** — freshness vs speed trade-offs (cache-aside, write-through, invalidation)
4. **Queues / Streams** — decoupling work (sync vs async, backpressure)
5. **Load Balancing** — intelligent work distribution
6. **Build classic systems** — rate limiters, chat apps, feeds — to get hands on the fundamentals
7. **Study real postmortems** — failure analysis is where judgment compounds

The sequence is deliberate. Each layer assumes you understand the layer below.

## Why "Backwards" Learning Fails

Starting with the architecture (microservices, event-driven, CQRS, etc.) before understanding the forces produces:
- **Cargo-cult adoption** — using Kafka because a blog post said to, without knowing what it solves
- **Premature optimization** — sharding before you have the load that justifies it
- **Inability to debug failures** — diagrams look fine until production shows you which assumptions were wrong
- **Bad interview answers** — name-dropping technologies instead of reasoning about trade-offs

## How to Use This Repo

The note structure in `system-design/` matches this sequence:
- Network → [load-balancer](load-balancer.md), [forward-reverse-proxy-and-api-gateway](forward-reverse-proxy-and-api-gateway.md), [service-discovery](service-discovery.md), [cdn](cdn.md)
- Storage → [database/](database/) — [indexing](database/indexing.md), [replication-and-sharding](database/replication-and-sharding.md), [redis](database/redis.md)
- Caching → [caching-strategies](caching-strategies.md)
- Queues/Streams → [event-driven](event-driven.md), [data-processing-patterns](data-processing-patterns.md)
- Failure modes (the postmortem reading) → [distributed-system-failure-modes](distributed-system-failure-modes.md)

For interview-style practice, see [leadership/system-design-interview-approach](../leadership/system-design-interview-approach.md).

## Related

- [Distributed system failure modes](distributed-system-failure-modes.md) — the "study real postmortems" step in concentrated form
- [Scaling fundamentals](scaling-fundamentals.md) — the forces behind scaling decisions
- [API concepts](api/api-concepts.md) — the requirements-driven framing for API design
- [System design interview approach](../leadership/system-design-interview-approach.md) — applying this in interviews

---

**Source:** https://newsletter.systemdesignclassroom.com/p/most-engineers-learn-system-design-backwards
**Date:** 2026-06-05
**Tags:** system-design, methodology, learning, constraints, trade-offs, fundamentals, design-triad
