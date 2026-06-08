# Netflix's Real-Time Distributed Graph (RDG)

How Netflix built a graph with **8 billion nodes, 150+ billion edges, 6 million writes/sec** on Cassandra — and why they rejected traditional graph databases for it.

## Key Takeaways

- **8B nodes, 150B+ edges, 2M reads/sec, 6M writes/sec, ~2,400 EC2 instances** — the scale at which Netflix needed a unified cross-product graph
- **Rejected traditional graph DBs** (Neo4j, AWS Neptune) for scalability limits; built **KVDAL** (Key-Value Data Abstraction Layer) on Cassandra with adjacency-list storage
- **Three-layer architecture**: Kafka (1M msgs/sec/topic) → Flink (filter/enrich/transform) → Data Mesh (5M+ records/sec) → KVDAL storage
- **27 namespaces across 12 Cassandra clusters** — separate per node/edge type for independent scaling and tuning
- The deeper lesson: **graphs are query patterns, not necessarily graph databases** — you can get graph-like access patterns with the right K/V schema at much better scale than purpose-built graph DBs

## What RDG Replaces

Netflix has many products (streaming, gaming, live events) historically running as independent microservices. Each has its own data store. Result: **siloed data, no unified view of a member's journey across products.**

Cross-domain analysis ("what does a user who streams AND games look like?") required:
- Manual joins across dozens of databases
- Stale snapshots from batch jobs
- Custom pipelines per question

RDG centralizes the cross-product graph so these queries are first-class.

## Why a Graph Representation

Two properties that drove the choice:

1. **Fast relationship traversals without expensive joins** — "members who watched X and played Y" is a 2-hop traversal in a graph; in relational schemas it's a multi-table JOIN
2. **Adapts to new connections without schema changes** — adding a new relationship type (e.g., "member subscribed to live event") is just a new edge type, not a schema migration

This is the standard "use a graph when relationships are first-class" argument. Where it gets interesting: **they didn't use a graph DB.**

## Why NOT a Traditional Graph DB

Netflix evaluated Neo4j and AWS Neptune. Both rejected on scalability:

- Neither cleanly horizontally scales to 8B nodes / 150B edges
- Neither hits Netflix's required write throughput (6M writes/sec) on cost-effective hardware
- Graph DB query languages (Cypher, Gremlin) are powerful but operational cost is high at extreme scale

The pragmatic alternative: **adjacency lists on a horizontally-scalable K/V store**. You give up some query expressiveness (no native multi-hop graph queries) in exchange for massive throughput.

## Architecture: Three Layers

```
Ingestion             →    Processing         →    Storage
─────────────────          ─────────────────        ─────────────────
Apache Kafka               Apache Flink             KVDAL (Key-Value
  - Up to 1M msgs/sec        - Filter, enrich,      Data Abstraction)
    per topic                  transform              - On Cassandra
                             - Output to Data         - Adjacency lists
                               Mesh (5M+ recs/sec)    - 27 namespaces
                                                      - 12 clusters
```

### Layer 1: Ingestion (Kafka)
Event streams from every Netflix product flow into Kafka topics. Each topic handles up to 1M messages/sec.

### Layer 2: Processing (Flink)
Flink jobs:
- Filter relevant events
- Enrich with additional context
- Transform events into graph operations (add node, add edge, update node properties)
- Output to Data Mesh (Netflix's data routing platform) at 5M+ records/sec

### Layer 3: Storage (KVDAL on Cassandra)

The clever part. KVDAL stores graph data as **adjacency lists**:

```
record_id = origin node ID
items[]   = destination nodes with edge properties
```

Example for "user 123 watched [movie A, movie B, movie C]":
```
record_id: user-123-watched
items: [
  { node: movie-A, timestamp: ..., progress: 0.85 },
  { node: movie-B, timestamp: ..., progress: 1.00 },
  { node: movie-C, timestamp: ..., progress: 0.32 }
]
```

A 1-hop traversal ("what did user 123 watch?") = one Cassandra read. A 2-hop traversal ("what did people who watched A also watch?") = scatter-gather.

### Independent Scaling per Edge Type

**27 separate namespaces** across **12 Cassandra clusters**. Each namespace can be tuned independently:
- Hot edge types (e.g., "watched" — billions per day) get more shards
- Cold edge types (e.g., "blocked" — rare) get fewer

This is the key operational lever — you don't have to tune one global graph for the worst case; you tune each relationship type for its actual access pattern.

## Performance

| | |
|---|---|
| Nodes | 8 billion |
| Edges | 150+ billion |
| Reads/sec | 2 million |
| Writes/sec | 6 million |
| Footprint | ~2,400 EC2 instances |

The write throughput is notable — most graph DBs collapse well below 6M/sec without aggressive sharding.

## The Generalizable Insight

> **Graphs are query patterns, not necessarily graph databases.**

If your "graph" workload is mostly:
- 1-hop or 2-hop traversals
- High write throughput
- Predictable access patterns (you know which edges you'll query)

...you may not need a graph DB at all. Adjacency lists on a horizontally-scalable K/V store (Cassandra, DynamoDB, ScyllaDB) get you the access pattern at much better scale.

Graph DBs win when:
- You need ad-hoc multi-hop queries (Cypher / Gremlin shine)
- Query patterns are unpredictable / exploratory
- Scale is small enough that graph DB operational overhead is fine

Netflix's workload matched the K/V pattern better — known queries, extreme scale, predictable hot edge types.

## Comparison to Existing Notes

| Note | How it relates |
|---|---|
| [Airtable vector search](airtable-vector-search-architecture.md) | Same pattern — domain-specific data store outperforms general-purpose one at scale |
| [Amazon Cosmo LLM recommendations](../../ai-ml-ds/concepts/amazon-cosmo-llm-recommendations.md) | LLM-generated knowledge graph at production scale |
| [Replication and sharding](database/replication-and-sharding.md) | Cassandra is the substrate; namespace-per-edge-type is a form of sharding |
| [Data processing patterns § stream](data-processing-patterns.md) | Flink as the canonical stream processor |

## Related

- [Replication and sharding](database/replication-and-sharding.md) — Cassandra's sharding model
- [Event-driven systems](event-driven.md) — Kafka + Flink is the canonical event processing stack
- [Data processing patterns](data-processing-patterns.md) — stream processing in depth
- [NotebookLM research workflow](../../ai-ml-ds/concepts/notebooklm-research-workflow.md) — different scale but similar "knowledge graph from many sources" pattern
- [Distributed system failure modes § fan-out](distributed-system-failure-modes.md#3-fan-out) — RDG enables fan-out queries at scale

---

**Source:** https://blog.bytebytego.com/p/how-netflix-built-a-real-time-distributed
**Date:** 2026-06-05
**Tags:** netflix, real-time-distributed-graph, rdg, cassandra, kvdal, kafka, flink, adjacency-list, graph, scale, case-study
