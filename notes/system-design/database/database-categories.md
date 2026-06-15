# Database Categories — A Practical Reference

Quick-reference table of the major database categories: representative engines, internal data structures, throughput, latency, and what each is best for. Use it to **pick the right DB tier** for a given workload, not to memorize features.

## Key Takeaways

- The right database is determined by **access pattern and consistency need**, not by "what we already have" — using the wrong category usually shows up as 100× latency or 10× cost
- **B+ trees + WAL** power transactional databases (RDBMS, Document); **LSM trees** power write-heavy stores (Cassandra, RocksDB, time-series); **HNSW/IVF** power vector search
- Latency varies by 6 orders of magnitude across categories — in-memory (<1ms) vs analytics columnar (sec–min). Mismatching tier to workload is the #1 perf bug
- **Polyglot persistence** is the modern default — one app uses 3–5 different DB categories. Resist the "one big database" pull
- Throughput ceilings cluster by data structure: hash tables → 1M+ ops/sec, B-trees → 100K, LSM writes → 1M, graph traversals → 100K

## The Big Table

| Category | Top 5 Engines | Internal Data Structure | Throughput | Latency | Best For |
|---|---|---|---|---|---|
| **RDBMS** | PostgreSQL, MySQL, Oracle, SQL Server, MariaDB | B+ Trees, Heap files, WAL | 1K–50K TPS | 1–10 ms | Transactions, joins, OLTP |
| **Document Store** | MongoDB, Couchbase, CouchDB, DocumentDB, ArangoDB | B-Trees / LSM Trees, JSON/BSON | 10K–100K ops/sec | 5–20 ms | Flexible JSON, APIs |
| **Key-Value Store** | Redis, DynamoDB, Riak, Aerospike, Etcd | Hash Tables, LSM Trees | 100K–1M+ ops/sec | 0.1–2 ms | Caching, fast lookup |
| **Columnar (OLAP)** | Snowflake, ClickHouse, BigQuery, Redshift, Vertica | Column files, Compression (RLE, Dictionary) | 100K–10M rows/sec | 100 ms–sec | Analytics, BI |
| **Wide Column Store** | Cassandra, HBase, ScyllaDB, Bigtable, Accumulo | LSM Trees, SSTables | 100K–1M writes/sec | 5–20 ms | Time-series, heavy writes |
| **Graph DB** | Neo4j, JanusGraph, TigerGraph, Neptune, ArangoDB | Adjacency Lists, Index-Free Adjacency | 1K–100K traversals/sec | 5–50 ms | Relationship queries |
| **In-Memory DB** | Redis, Memcached, SAP HANA, Hazelcast, VoltDB | Hash Tables, Skip Lists | 500K–5M ops/sec | <1 ms | Ultra-low latency |
| **Geospatial DB** | PostGIS, MongoDB Geo, Elasticsearch Geo, BigQuery GIS, Neo4j Spatial | R-Trees, Quadtrees, Geohashes | 10K–100K qps | 10–100 ms | Location queries |
| **Immutable Ledger** | QLDB, Hyperledger, Ethereum, Corda, BigchainDB | Merkle Trees, Append-only Logs | 100–5K TPS | 10 ms–sec | Audit & compliance |
| **Text Search Engine** | Elasticsearch, OpenSearch, Solr, Meilisearch, Typesense | Inverted Index, FSTs | 10K–500K qps | 10–100 ms | Full-text search |
| **Blob / Object Store** | S3, GCS, Azure Blob, MinIO, Ceph | Object Index + Chunked Files | GBs/sec | 10–100 ms | Unstructured data |
| **Vector DB** | Pinecone, Weaviate, Milvus, FAISS, Chroma | HNSW, IVF, PQ | 1K–100K qps | 10–100 ms | Semantic search |
| **Data Lake** | Delta Lake, Iceberg, Hudi, BigLake, S3 + Athena | Parquet/ORC, Metadata Trees | TB batch | sec–min | Large-scale analytics |
| **Time-Series (TSDB)** | InfluxDB, Prometheus, TimescaleDB, VictoriaMetrics, OpenTSDB | LSM Trees, Time-partitioned blocks, Compression (Gorilla, Delta-of-Delta) | 100K–5M points/sec | 1–10 ms write, 10–100 ms query | Metrics, monitoring, IoT, observability |

## How to Pick

A simple decision tree:

1. **Need transactions across rows?** → RDBMS (or distributed SQL like CockroachDB)
2. **Append-only events with time-bucketed reads?** → Time-series DB
3. **Heavy write load, queryable by partition key?** → Wide-column (Cassandra)
4. **Sub-ms read latency for small values?** → In-memory KV (Redis)
5. **Analytical queries over billions of rows?** → Columnar OLAP (ClickHouse, BigQuery)
6. **Semantic search on embeddings?** → Vector DB
7. **Relationship-heavy traversal (friends of friends)?** → Graph DB
8. **Document-shaped data with flexible schema?** → Document store
9. **Audit trail with immutability + crypto-verifiable history?** → Ledger DB
10. **Large opaque blobs (images, video, files)?** → Object store

## See Also

- [replication-and-sharding.md](replication-and-sharding.md) — how to scale any of these horizontally
- [cloud-native-data-architecture.md](cloud-native-data-architecture.md) — polyglot persistence in practice
- [data-warehouse-vs-data-lake-vs-data-mesh.md](data-warehouse-vs-data-lake-vs-data-mesh.md) — analytical-side architecture choices
- [indexing.md](indexing.md) — the index data structures referenced above
- [redis.md](redis.md) — the canonical in-memory KV
- [../../ai-ml-ds/concepts/vector-databases.md](../../ai-ml-ds/concepts/vector-databases.md) — vector DB internals

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** databases, rdbms, nosql, document-store, key-value, columnar, graph, time-series, vector-db, polyglot-persistence, database-selection
