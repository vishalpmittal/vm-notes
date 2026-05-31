# Vector Databases

## Key Takeaways

- Vector databases solve the O(n) brute-force problem of similarity search by using specialized indexes like HNSW that reduce lookup to O(log n)
- Architecture has three layers: storage (vectors + metadata, quantization), indexing (HNSW graph), and query (embedding, filtering, ranking)
- Quantization compresses 32-bit float vectors to 8-bit integers or binary, cutting storage 4x+ with acceptable accuracy loss
- HNSW (Hierarchical Navigable Small World) is the dominant production algorithm -- it navigates a multi-layer graph from sparse (coarse) to dense (fine) layers
- For small datasets (<100K vectors), pgvector on PostgreSQL is sufficient; dedicated vector DBs become necessary at scale

## The Problem: Why Not a Regular Database?

Traditional databases use indexes (B-trees, hash) for exact-match lookups via WHERE clauses. Embeddings break this model because there is no exact value to match -- you need to find the *closest* vectors by meaning.

Without specialized indexing, finding similar vectors requires comparing the query against every vector in the database -- **O(n) time complexity**. At scale: a query taking 1 second with 10,000 vectors becomes 16 minutes with 10 million vectors.

![Brute-force search scales linearly](../../images/20260531-1232-vecdb-bruteforce-search.png)

## Embeddings and Semantic Search

An **embedding** is a list of numbers representing the semantic meaning of data. Similar meaning produces similar numerical values. A 1,536-dimensional vector using 32-bit floats requires ~6KB of storage.

![Embedding vectors cluster by meaning in high-dimensional space](../../images/20260531-1231-vecdb-embedding-space.png)

**Semantic search** understands intent rather than keywords. A query for "how do I log in" retrieves results about "account access" and "sign in" even without exact word matches.

![Semantic search maps queries to related concepts](../../images/20260531-1230-vecdb-semantic-search.png)

## Three-Layer Architecture

Vector databases are organized into three layers, each with distinct responsibilities and tradeoffs.

![Vector DB 3-layer architecture: query, index, storage](../../images/20260531-1235-vecdb-architecture-layers.png)

### Storage Layer

Responsible for persisting vectors and metadata. Key techniques:

- **Quantization:** Reduces precision from 32-bit floats to 8-bit integers or binary. Cuts storage 4x+ while introducing acceptable approximation error.
- **Memory vs. Disk:** Tiered storage keeps frequently accessed vectors in memory (fast, expensive) while cold data resides on disk (slow, cheap).

![Quantization reduces float precision for compression](../../images/20260531-1233-vecdb-quantization.png)

### Indexing Layer (HNSW)

**HNSW (Hierarchical Navigable Small World)** is the dominant algorithm in production vector databases. It creates a multi-layer graph:

- **Layer 1 (sparse):** Coarse navigation across the full dataset -- like a country-level map
- **Layer 2 (medium):** Narrows search to relevant regions -- like a city-level map
- **Layer 3 (dense):** Identifies closest neighbors -- like a street-level map

![HNSW navigates through sparse-to-dense layers in O(log n)](../../images/20260531-1234-vecdb-hnsw-layers.png)

Key tuning parameters:

| Parameter | What It Controls | Higher Value Effect |
|-----------|-----------------|-------------------|
| **M** | Connections per vector | Better recall, more memory, slower inserts |
| **ef** (efSearch) | Exploration factor at query time | Better accuracy, slower queries |
| **efConstruction** | Exploration factor at build time | Better index quality, slower builds |

The core insight: you don't search everything, you *navigate*. Approximate nearest neighbor is "close enough" -- trading perfect recall for orders-of-magnitude speed improvement.

### Query Layer

The query execution pipeline:

1. **Embed** the query into a vector
2. **Traverse** the HNSW index (coarse to fine)
3. **Calculate** distances to candidate vectors
4. **Rank** and return top-K results

![Query flow through vector database layers](../../images/20260531-1236-vecdb-query-flow.png)

## Production Tradeoffs

Every operational decision in a vector database involves a tradeoff:

| Decision | Tradeoff |
|----------|----------|
| Quantization level | Storage cost vs. accuracy |
| Memory vs. disk | Query speed vs. infrastructure cost |
| HNSW M parameter | Recall quality vs. memory + insert speed |
| ef parameter | Query accuracy vs. latency |
| Index rebuild frequency | Recovery speed vs. data freshness |

## When to Use What

- **pgvector (PostgreSQL extension):** Good for <100K vectors with moderate query volume. Simplifies infrastructure by keeping vectors alongside relational data. Hits scalability limits with millions of vectors.
- **Dedicated vector database (Pinecone, Weaviate, Milvus, Qdrant):** Required when dataset size grows into millions+, query latency requirements tighten, or you need advanced features like filtered search, hybrid search, or multi-tenancy.

---

**Source:** https://newsletter.systemdesign.one/p/what-is-a-vector-database
**Date:** 2026-05-31
**Tags:** vector-database, embeddings, hnsw, similarity-search, quantization, semantic-search, pgvector
