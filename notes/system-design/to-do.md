# System Design TODO

Topics from `/Users/vimittal/Downloads/VM/system-design.html` that don't yet have notes in `notes/system-design/`. Items already covered have been omitted.

> Format: `[ ] Topic — short note on what to cover`. Add a `**target file:**` line when you've decided where it should live.

## Database

- [ ] **Materialized Views** — when to use, refresh strategies (on-demand / scheduled / triggered), trade-offs vs regular views vs caching. Often paired with CQRS read models
- [ ] **Partitioning (general)** — covered partially in `database/replication-and-sharding.md` but partitioning ≠ sharding; horizontal vs vertical, partition keys, when partition without sharding
- [ ] **DB Triggers** — what they're good for, why they're often a bad fit (hidden logic, debugging pain), modern alternatives (CDC, application logic)
- [ ] **LSM Trees** — write-amplification math, when LSM beats B-tree (write-heavy), examples (Cassandra, ScyllaDB, RocksDB, LevelDB). [Flagged earlier as a gap]
- [ ] **WAL (Write-Ahead Logs)** — mentioned in `replication-and-sharding.md` and `cdc.md` but no standalone note. Recovery, replication, CDC all build on WAL
- [ ] **Isolation Levels** — Read Uncommitted, Read Committed, Repeatable Read, Serializable, Snapshot. ACID-I in depth; phenomena (dirty read, non-repeatable read, phantom read)
- [ ] **Leader Election Algorithms** — Bully, Ring, Paxos, Raft, Zookeeper Atomic Broadcast (ZAB). Where each is used in production systems
- [ ] **Data Models** — Flat (Excel), Hierarchical (XML/JSON), Relational (Postgres/MySQL), Star (Redshift/Teradata), Snowflake (Snowflake/Redshift), Network/Graph (Neo4j). When each fits
- [ ] **BASE properties** — Basically Available, Soft State, Eventual Consistency. The NoSQL counterpoint to ACID. Mostly in `cap.md` but worth focused treatment

## Message Brokers & Queues

- [ ] **Kafka** — log-structured architecture, partitions, consumer groups, retention. Reddit and Datadog case studies use it; no fundamentals note
- [ ] **RabbitMQ** — vs Kafka, exchanges/queues/bindings, when each wins
- [ ] **Kinesis** — AWS-native streaming, comparison to Kafka
- [ ] **Locking Queues** — single-consumer semantics, when needed
- [ ] **Priority Queues** — implementation strategies, fairness vs strict priority, starvation

## Architecture Patterns

- [ ] **Orchestration vs Choreography** — explicit central coordinator vs event-driven peer coordination. Trade-offs, common cases. Touched in event-driven and multi-agent but not standalone
- [ ] **API Composition** — when API gateway aggregates multiple service calls vs client-side composition vs BFF
- [ ] **Backend for Frontend (BFF)** — per-client API layer (web vs iOS vs Android), trade-offs
- [ ] **Hexagonal / Onion / Clean Architecture** — ports & adapters, dependency direction, when worth the structure
- [ ] **Repository pattern + Unit of Work** — data-access abstraction, transaction boundaries
- [ ] **MVC / MVVM / Flux / Redux** — UI architecture patterns, where each fits
- [ ] **Bulkhead pattern** — resource isolation, blast-radius containment (mentioned in `distributed-system-failure-modes.md` and `scaling-fundamentals.md` but no focused treatment)
- [ ] **Plugin / Microkernel / Micro Frontend / Serverless / Reactive** — modular and cloud-scale variants
- [ ] **DDD integration patterns** — Anti-Corruption Layer, Published Language, Open Host Service, Conformist, Shared Kernel, Customer-Supplier, Separate Ways
- [ ] **Transactional Outbox** — covered partially in `event-driven.md` but worth a focused note paired with dual-write problem
- [ ] **GoF Design Patterns** — Gang-of-Four object-oriented design patterns; could go in `programming/` rather than `system-design/`

## Networking

- [ ] **Network protocols overview** — TCP/IP, UDP, HTTP, HTTP/3 (QUIC), WebSocket, SMTP, FTP. Quick reference with when to use each
- [ ] **UDP deep dive** — when UDP beats TCP (live video, DNS, multicasting, IoT), risks
- [ ] **HTTP/3 + QUIC** — multiplexing without head-of-line blocking, why it matters
- [ ] **NginX** — reverse proxy + load balancer + cache, common config patterns
- [ ] **Load balancer algorithms** — Least Connection, Least Response Time, Least Bandwidth, Round Robin, Sticky Round Robin, Weighted RR, IP Hash, Consistent Hashing (some in `load-balancer.md` — verify coverage and enrich if needed)

## Reliability / Retry Patterns

- [ ] **Retry pattern variants** — Immediate, Fixed Interval, Exponential Backoff, Exp Backoff + Jitter, Retry with Deadline/Budget, Selective Retry, Retry with Fallback, Hedged Requests, Queue-Based Retry. Most are in `api/retries.md`; verify the full list is enumerated
- [ ] **Hedged Requests** — specifically: send duplicate request after timeout, take first response. Used at Google/Netflix for tail-latency reduction

## Hashing

- [ ] **ID generation strategies** — Snowflake IDs, UUID v4/v7, KSUID, ULID — collision properties, sortability, distributed generation

## Data Processing

- [ ] **Micro-batching** — between stream and batch, when it wins. Touched in `data-processing-patterns.md` (Spark Streaming)
- [ ] **ETL patterns** — vs ELT, modern ELT pipelines (Fivetran, dbt), batch vs stream
- [ ] **Ceph** — distributed object/block/file storage (odd placement in original CSV; really infrastructure)

## Observability

- [ ] **Centralized logging (ELK stack)** — Elasticsearch + Logstash + Kibana, alternatives (Loki, Datadog Logs)
- [ ] **Metrics + Prometheus + Grafana** — pull-based metrics, PromQL, alerting patterns
- [ ] **Distributed tracing** — OpenTelemetry, Jaeger, span propagation. Mentioned across notes but no focused note
- [ ] **Alerting patterns** — symptom-based vs cause-based, on-call hygiene, alert fatigue prevention

## Security (gaps)

Most security topics are well covered. Remaining gaps:

- [ ] **Disaster Recovery** — RPO/RTO, backup strategies, hot/warm/cold standby, multi-region failover
- [ ] **KMS / Vault / DLP** — key management at scale, secret rotation, data loss prevention
- [ ] **Firewall + Network Security** — L3/L4 vs L7, WAF, security groups, zero-trust networking
- [ ] **Infrastructure Security** — secure boot, hardened images, CIS benchmarks
- [ ] **Audit and Compliance** — SOC 2, PCI-DSS, GDPR data lineage, audit log requirements
- [ ] **Encoding vs Encryption vs Tokenization** — note: existing `hashing-encryption-tokenization.md` covers hashing/encryption/tokenization but NOT encoding (Base64, URL encoding). Add encoding section
- [ ] **HTTPS / SSL / TLS handshake** — record/handshake layers, mutual TLS, cert chains. Partially in service-mesh, common-cyber-attacks
- [ ] **Terminal Security** — endpoint hardening (this is more devops than system design)

## Misc / Theory

- [ ] **SOLID principles** — partially in `programming/oop-concepts.md` (cross-reference exists). Could fold into that note or split out
- [ ] **Common System Design Problems cheat-sheet** — the 8-problem table you sent earlier; could become its own quick-reference note mapping problems → existing notes
- [ ] **PIE theorem** — Performance / Infrastructure / Experience trade-off (clarify what this refers to — possibly Pat Helland or different framing). Verify before writing
- [ ] **MVP (Model-View-Presenter)** — UI architecture; goes with MVC/MVVM
- [ ] **KISS principle** — philosophy, not a design pattern. May not warrant its own note
- [ ] **Common data structures** — List, Array, Stack, Queue, Heap, Tree, Trie, Graph, R-tree, Hash table. CS fundamentals — better fit for `programming/` than `system-design/`

## Priority Ranking (Highest First)

If you only do a few, the highest-ROI items based on (a) genuine repo gaps and (b) frequency of real production relevance:

1. **LSM Trees** — recurring topic in modern DB design; fills a real gap
2. **Isolation Levels** — ACID-I is constantly relevant in any DB conversation
3. **Kafka fundamentals** — referenced everywhere in this repo; no dedicated note
4. **Distributed tracing (OpenTelemetry)** — gap in observability/
5. **Hexagonal / Clean Architecture** — common in interview + real-world architecture discussions
6. **Disaster Recovery (RPO/RTO)** — recurring in production system-design conversations
7. **Hedged Requests** — small but valuable; complements latency-throughput-bandwidth.md
8. **Centralized logging (ELK / Loki)** — flagged earlier as a real gap
9. **ID generation (Snowflake/ULID)** — comes up constantly in distributed system design
10. **Leader election algorithms** — Raft/Paxos overview note useful for distributed-systems literacy

---

**Source:** Migrated from `/Users/vimittal/Downloads/VM/system-design.html` (personal study TODO list)
**Date:** 2026-06-07
**Status:** Living document — check off as notes are written; remove items if you decide they're not worth covering
