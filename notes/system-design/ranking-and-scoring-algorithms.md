# Ranking and Scoring Algorithms

Core algorithms used in search, recommendation, and feed-ranking systems — from classical IR to modern ML.

## Key Takeaways

- **TF-IDF** and **BM25** remain the baseline for text relevance — BM25 is the modern default, fixing TF-IDF's two main weaknesses (term saturation and length bias)
- **Cosine similarity** is the workhorse for vector spaces — semantic search, embeddings, and recommendation systems all run on it
- **PageRank** ranks by *graph structure* (link quality + quantity) — generalizes to any "trust propagates through references" problem
- **Learning to Rank (LTR)** layers ML on top: feature-engineered ranking that adapts to user behavior signals — the default for production search systems at scale

## The Classical Stack

| Algorithm | What it measures | When it wins |
|---|---|---|
| **TF-IDF** | Term frequency in a doc × how rare the term is across the corpus | Baseline relevance scoring; quick to compute |
| **BM25** | TF-IDF + **term saturation** (cap on max term hits) + **document length normalization** | Modern text search default (Elasticsearch, Solr); fixes TF-IDF's bias toward long docs |
| **Cosine similarity** | Cosine of the angle between two vectors (query vector vs doc vector) | High-dimensional semantic search; embedding-based retrieval |
| **PageRank** | Rank by inbound link quantity *and* quality (recursive) | Web search; any "trust propagates through references" graph |
| **Learning to Rank (LTR)** | ML model trained on user-behavior features (clicks, dwell time, purchases, etc.) | Production search/recommendation at scale where business signals matter more than text relevance |

## Why BM25 Beats TF-IDF

Two specific fixes:

1. **Term saturation** — TF-IDF treats "appears 100 times" as 100× more relevant than "appears 1 time." BM25 caps the contribution of repeated terms (a doc that mentions a word once vs. 50 times is *not* 50× more relevant)
2. **Document length normalization** — TF-IDF inflates scores for long documents (more chances for term matches). BM25 normalizes by length so short, focused docs aren't penalized

## When to Reach for Each

- **Pure text search, no labels:** BM25
- **Semantic / embedding-based:** Cosine similarity over vectors (often layered *after* a BM25 retrieval pass)
- **Reputation/authority graphs:** PageRank
- **You have user behavior data and want it to drive ranking:** LTR (often a re-ranker on top of BM25 + cosine candidates)

Most modern systems are **hybrid**: a fast lexical pass (BM25) generates candidates, a vector model (cosine) re-ranks for semantic relevance, and an LTR model produces the final ordering using behavior features.

## See Also

- [database/indexing.md](database/indexing.md) — the storage side of how BM25 / inverted indexes work
- [ai-ml-ds/concepts/vector-databases.md](../ai-ml-ds/concepts/vector-databases.md) — where cosine similarity lives in modern stacks
- [ai-ml-ds/concepts/rag.md](../ai-ml-ds/concepts/rag.md) — hybrid lexical+vector retrieval in practice

---

**Source:** /Users/vimittal/Downloads/prep/prep.html
**Date:** 2026-06-13
**Tags:** ranking, search, tf-idf, bm25, cosine-similarity, pagerank, learning-to-rank, recommendation
