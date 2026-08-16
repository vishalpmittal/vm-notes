# system-design/database — data stores & data architecture

Data stores, replication, CDC, indexing, event sourcing, and data-platform architecture. Company database writeups go in case-studies/ (see Subcategories); general distributed-systems concepts go in the parent folder.

## Notes
- [cdc.md](cdc.md) — change data capture: tailing transaction logs into change-event streams
- [cloud-native-data-architecture.md](cloud-native-data-architecture.md) — storage/compute disaggregation, multitenancy, build-vs-buy spectrum
- [data-warehouse-vs-data-lake-vs-data-mesh.md](data-warehouse-vs-data-lake-vs-data-mesh.md) — three approaches to organizing analytical data
- [database-categories.md](database-categories.md) — reference table matching DB categories to access patterns and workloads
- [event-sourcing-cqrs.md](event-sourcing-cqrs.md) — event log as source of truth; separating read and write models
- [indexing.md](indexing.md) — how indexes work, B-tree vs specialized types, write costs
- [redis.md](redis.md) — in-memory store, single-threaded atomicity, data structures, persistence
- [refactoring-databases.md](refactoring-databases.md) — expand/contract pattern for safe live schema evolution
- [replication-and-sharding.md](replication-and-sharding.md) — replication scales reads, sharding scales writes; shard keys, WAL

## Subcategories
- [case-studies/](case-studies/README.md) — company database writeups (Agoda, AWS S3, Datadog, Nextdoor)
