# system-design — architecture & distributed-systems concepts

General architecture and distributed-systems notes live directly here. Company writeups, API design, data stores, observability, and security each have their own subfolder (see Subcategories). Route a new note to the narrowest folder that fits.

## Notes
- [caching-strategies.md](caching-strategies.md) — five cache patterns (cache-aside, write-through/behind) and cache-stampede failure mode
- [cap.md](cap.md) — consistency models spectrum, quorums, per-workflow consistency choices in distributed systems
- [cdn.md](cdn.md) — content delivery networks, edge caching, invalidation, and edge compute
- [circuit-breakers.md](circuit-breakers.md) — closed/open/half-open state machine to prevent cascading failures
- [cross-cutting-concerns.md](cross-cutting-concerns.md) — logging, config, discovery, resilience patterns shared across microservices
- [data-processing-patterns.md](data-processing-patterns.md) — MapReduce, stream processing, Lambda and Kappa architectures
- [distributed-system-failure-modes.md](distributed-system-failure-modes.md) — dual-write, gray failures, partitions, cascades, and the gaps between components
- [distributed-transactions.md](distributed-transactions.md) — 2PC, Saga, vector clocks for atomicity across nodes
- [event-driven.md](event-driven.md) — idempotency, ordering, dual-write, and correctness in event-driven systems
- [fan-out-patterns.md](fan-out-patterns.md) — push vs pull vs hybrid delivery of one event to many recipients
- [forward-reverse-proxy-and-api-gateway.md](forward-reverse-proxy-and-api-gateway.md) — forward proxy, reverse proxy, and API gateway roles compared
- [hot-path-design.md](hot-path-design.md) — scale what is hot; separate display reads, decision reads, and writes
- [jvm.md](jvm.md) — JVM pipeline: class loading, linking, memory areas, JIT execution
- [kafka.md](kafka.md) — distributed append-only log: topics, partitions, brokers, consumer groups, acks
- [latency-throughput-bandwidth.md](latency-throughput-bandwidth.md) — distinguishing the three network-performance metrics
- [learning-system-design.md](learning-system-design.md) — methodology: start from constraints and trade-offs, not diagrams
- [load-balancer.md](load-balancer.md) — request lifecycle, health checks, NAT, TLS termination
- [locking.md](locking.md) — optimistic vs pessimistic locking under concurrency and contention
- [microservices.md](microservices.md) — ownership/independence, when to adopt, and common anti-patterns
- [mobile-system-design.md](mobile-system-design.md) — offline-first, sync, batching, and mobile-specific constraints
- [multi-region-architecture.md](multi-region-architecture.md) — active-passive vs active-active, replication, and cross-region consistency
- [payment-systems.md](payment-systems.md) — correctness-under-failure, PSP integration, idempotency, double-entry ledger
- [ranking-and-scoring-algorithms.md](ranking-and-scoring-algorithms.md) — TF-IDF, BM25, cosine similarity, PageRank, learning-to-rank
- [rate-limiting.md](rate-limiting.md) — algorithms, state location, and sync vs async counting trade-offs
- [scaling-fundamentals.md](scaling-fundamentals.md) — horizontal vs vertical, auto-scaling, connection pooling
- [service-discovery.md](service-discovery.md) — client-side vs server-side registries for resolving live endpoints
- [service-mesh-and-sidecar.md](service-mesh-and-sidecar.md) — sidecar proxies and service mesh for cross-cutting concerns at scale
- [sync-vs-async-communication.md](sync-vs-async-communication.md) — when to block vs defer; grouping keys and ordering trade-offs
- [to-do.md](to-do.md) — working list of system-design topics not yet written up

## Subcategories
- [api/](api/README.md) — API design, real-time comms, REST/GraphQL/gRPC, retries
- [case-studies/](case-studies/README.md) — company system-design writeups (Netflix, Airbnb, etc.)
- [database/](database/README.md) — data stores, replication, CDC, indexing, event sourcing
- [observability/](observability/README.md) — logging, tracing, SLA/SLO/SLI, debugging
- [security/](security/README.md) — appsec, auth, crypto, cyber attacks
