# Multi-Model AI Pipelines

## Key Takeaways

- Specialized models consistently outperform generalists — but they produce fundamentally different data types (text labels, vector embeddings, timestamps), making the fusion layer the hardest engineering challenge
- Netflix processes 216M frames per season through multiple specialized models, then fuses outputs into 1-second temporal buckets for sub-second search
- The core insight: "model capability matters less than the pipeline architecture enabling discovery and retrieval at scale"
- Hybrid search (keyword matching + vector similarity) enables queries like "Joey in the kitchen" that combine proper nouns with semantic concepts

## Netflix Video Search Architecture

![Multiple specialized models process video frames into annotations fused via Kafka](../../images/20260524-1831-multimodal-ai-specialized-models.png)

**Why multiple models?** Each excels at a distinct task:

- Character recognition → text labels ("Joey")
- Scene classification → 512-dim vector embeddings
- Dialogue transcription → timestamped text
- Object detection → visual element identification

### Three-Stage Pipeline

![Three-stage pipeline: persist → fuse → index](../../images/20260524-1832-multimodal-ai-temporal-fusion.png)

1. **Transactional persistence** — Raw annotations stored in Cassandra with zero transformation; no computation slows real-time ingestion
2. **Offline data fusion** — Async job normalizes outputs into 1-second temporal buckets, intersects annotations across models, writes enriched records with composite keys (asset ID + time bucket)
3. **Real-time search indexing** — Elasticsearch ingests buckets as nested documents enabling cross-annotation queries within the same time interval

### Temporal Bucketing

![Temporal bucketing and fusion — how overlapping model outputs merge into 1-second fused records](../../images/20260524-1833-multimodal-ai-search.png)

- Continuous model detections become discrete 1-second intervals
- Multiple models' outputs for the same bucket merge into one record
- 2,000 hours of footage → 7.2M temporal buckets

### Search

- **Keyword matching** for proper nouns (character names)
- **Vector similarity** for semantic concepts ("kitchen")
- Users choose exact kNN (optimal but expensive) vs. approximate NN (faster, acceptable accuracy loss)
- Results aggregated into clips with union mode (any feature) or intersection mode (all features co-occur)

### Tradeoffs

| Decision | Chose | Gave Up |
|---|---|---|
| Offline fusion | Throughput, consistency | Instant searchability for new content |
| Approximate NN | Speed at scale | Some relevance precision |
| Ensemble of specialists | Per-task accuracy | Simpler infrastructure |

Netflix is simultaneously developing both ensemble pipelines and a unified foundation model (MediaFM) — both approaches remain viable.

---

**Source:** https://blog.bytebytego.com/p/how-netflix-is-using-multimodal-ai
**Date:** 2026-05-24
**Tags:** multimodal-ai, ml-pipeline, video-search, netflix, embeddings, elasticsearch
