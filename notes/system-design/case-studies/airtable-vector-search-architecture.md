# Airtable Vector Search Architecture

## Key Takeaways

- A single upstream data property — only **~25% of Airtable bases see activity in any given week** — cascades through every architectural choice (partitioning, memory layout, recovery)
- Per-tenant (one-base-per-partition) isolation breaks down past ~100K partitions per Milvus collection; Airtable used **hierarchical capping** (400 collections × 1,000 partitions = 400K bases per cluster) to keep partition creation under 20ms and load times sub-30s
- Index choice was driven by a hard SLO: **HNSW beat IVF-SQ8 (recall) and DiskANN (disk-I/O latency)** because the system had to meet 500ms p99 with high recall, even at memory cost
- **Hot/cold partition offload to object storage** is the memory-cost lever, only viable because of the bimodal access pattern; cold bases get a brief warm-up delay rather than failure
- Recovery is **"re-embed from source"** (lazy, most-used-first) instead of backup/restore — reuses the existing async embedding pipeline and uniformly handles corruption, model migrations, and data residency moves

## Problem and Constraints

Airtable needed a semantic-search layer to power AI features (Omni natural-language queries, linked-record recommendations) across millions of small, multi-tenant customer bases — under four hard constraints:

| Constraint | Requirement |
|---|---|
| Latency | p99 ≤ 500ms |
| Throughput | High write volume from constantly mutating customer data |
| Scale | Horizontal scaling to millions of bases |
| Privacy | Self-hosted (data residency requirement) |

These constraints ruled out specialized managed vector DBs (Pinecone), in-memory-only options that don't shard cleanly, and disk-heavy options that miss the latency SLO.

## Architecture Overview

- **Vector store:** Milvus, self-hosted
- **Tenancy model:** one Milvus partition per customer base (strong isolation, cheap deletion, no per-query tenant filtering)
- **Scaling shape:** hierarchical — clusters → collections → partitions
- **Index:** HNSW (in-memory graph index)
- **Memory tier:** hot partitions resident in RAM; cold partitions offloaded to object storage, reloaded on demand
- **Write/index pipeline:** async embedding pipeline already handles incremental updates; reused as the recovery mechanism

## Partitioning Strategy

**One Milvus partition per customer base** gives:

- Strong isolation — no cross-tenant query bleed
- Cheap deletion — drop a partition, not a filtered table sweep
- No per-query tenant filtering overhead

### The 100K Partition Wall

A single Milvus collection started degrading past ~100K partitions:

| Partitions per collection | Partition creation latency | Load time |
|---|---|---|
| < 100K | ~20ms | < 30s |
| > 100K | ~250ms | > 30s |

### Hierarchical Capping (the fix)

```
Cluster
├── Collection 1 (≤ 1,000 partitions)
├── Collection 2 (≤ 1,000 partitions)
├── ...
└── Collection 400 (≤ 1,000 partitions)
```

= 400,000 bases per cluster. Beyond that, add another cluster.

Partition creation and load latency stay predictable as customer base grows.

## Index Selection

| Index | Strength | Weakness | Verdict |
|---|---|---|---|
| **HNSW** | Fast, high recall | RAM-heavy | ✅ Chosen |
| **IVF-SQ8** | Compressed (1 byte/dim), low memory | Lower recall | ❌ Recall too low |
| **DiskANN** | Massive scale, low memory | Higher latency (disk I/O) | ❌ Misses 500ms p99 |

The 500ms SLO ruled out DiskANN; result-quality requirements ruled out IVF-SQ8. HNSW's memory cost was acceptable given the hot/cold offload strategy below.

## Hot/Cold Memory Optimization

Key insight: **roughly 75% of bases are idle in any given week.** This bimodal access pattern enables aggressive cost optimization:

- **Hot partitions** (active 25%) — resident in RAM for fast HNSW search
- **Cold partitions** (idle 75%) — offloaded to cheap object storage, reloaded within seconds when accessed

Cold-access cost is a brief warm-up delay, not a failure. Memory footprint shrinks ~4x compared to keeping everything hot.

**This only works because access is bimodal.** If 100% of bases were active 100% of the time, "the hot set would be the entire dataset" and the offload strategy collapses.

## Recovery: Re-Embed From Source

Traditional backup/restore is *not* used. Instead:

1. Spin up fresh clusters
2. Re-embed customer data from canonical source (the Airtable bases themselves)
3. Prioritize most-used bases; lazy-load the rest

Why this works:

- The async embedding pipeline already exists for writes — recovery is just running it backward
- Doubles as the migration path for **embedding model upgrades** (re-embed everything with the new model)
- Doubles as the migration path for **data residency changes** (re-embed in a new region)
- Eliminates a whole class of backup-staleness and restore-corruption bugs

## Architectural Lessons

> "Changing any one of those properties can cause the design to fall apart."

The architecture is tightly coupled to data-shape assumptions:

- Bimodal access pattern → hot/cold offload works
- High write throughput → async embedding pipeline → cheap recovery
- Per-tenant isolation → one-partition-per-base
- Latency-critical → HNSW over DiskANN

Swap Milvus for another vector DB, or HNSW for another in-memory index, and the design still holds. **The transferable lesson is letting data properties dictate architecture, not the specific tech.**

## See Also

- [instacart-search-infrastructure.md](instacart-search-infrastructure.md) — a sibling search case study, but fundamentally different problem (lexical → semantic migration, ES vs pgvector for product search)
- [../ai-ml-ds/concepts/vector-databases.md](../../ai-ml-ds/concepts/vector-databases.md) — HNSW fundamentals and vector DB tradeoffs

---

**Source:** https://blog.bytebytego.com/p/how-airtable-built-the-search-layer
**Date:** 2026-06-01
**Tags:** system-design, vector-search, semantic-search, milvus, hnsw, multi-tenancy, partitioning, airtable, embeddings, ai-infrastructure
