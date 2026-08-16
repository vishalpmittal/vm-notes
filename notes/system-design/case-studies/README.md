# system-design/case-studies — company system-design writeups

Real-world architecture case studies from named companies. For company database-specific writeups use database/case-studies/; for general concepts use the parent folder.

## Notes
- [airbnb-local-payments.md](airbnb-local-payments.md) — config-driven, processor-agnostic platform for 20+ local payment methods
- [airtable-vector-search-architecture.md](airtable-vector-search-architecture.md) — Milvus partitioning, HNSW, hot/cold offload driven by bimodal access
- [instacart-search-infrastructure.md](instacart-search-infrastructure.md) — unifying keyword and semantic search on Postgres + pgvector
- [netflix-live-streaming.md](netflix-live-streaming.md) — Live Origin, dual encoding, rate limiting for 65M concurrent streams
- [netflix-real-time-distributed-graph.md](netflix-real-time-distributed-graph.md) — 8B-node graph on Cassandra via KVDAL adjacency lists
- [pinterest-pacer-async-compute.md](pinterest-pacer-async-compute.md) — async job platform with Helix partition ownership, worker isolation
- [reddit-kafka-migration.md](reddit-kafka-migration.md) — petabyte-scale Kafka move from EC2 to Kubernetes, zero downtime
- [spotify-release-pipeline.md](spotify-release-pipeline.md) — trunk-based dev, rollout rings, feature flags for weekly releases
- [wise-tech-stack.md](wise-tech-stack.md) — internal platform as product, microservice chassis, canary deploys
