# ML Inference at Scale

## Key Takeaways

- At scale, "most of the cost lives outside the model itself" — serialization, feature fetching, and data plane dominate, not model computation
- Latency is the harder constraint, not throughput: "a billion predictions/sec is parallelism; 100ms per prediction is an architecture problem"
- CPU/GPU compute split (embeddings on CPU, dense math on GPU) prevents wasting GPU memory on lookup tables
- Feature collocation (caching document features on inference instances) eliminates network fanout for high-throughput scoring
- Train-serve skew is the central operational concern — dual feature stores (offline + online) with continuous monitoring address it

## Snapchat Bento Platform

474M daily active users, 1B+ predictions/sec, 100ms latency budget per request.

![Bento system architecture — end-to-end ML platform](../../images/20260524-1841-inference-bento-training.png)

### The Core Problem

One user opens the app → platform must score millions of candidates (content, ads, friends, AR lenses). A single request creates hundreds or thousands of (user, candidate) pairs requiring model scoring.

### Ad Ranking Flow

![Ad ranking flow — eligibility, light models, heavy models, auction](../../images/20260524-1840-inference-bento-overview.png)

### Training Pipeline

![Training workflow — data gen, training, evaluation, export](../../images/20260524-1842-inference-bento-feature-store.png)

Three layers: core TF/Keras framework → user model code → YAML training config. Model export splits computation: embedding lookups on CPU, dense matrix ops on GPU.

### Feature Store (Robusta)

![Feature and training data generation via Robusta](../../images/20260524-1843-inference-bento-serving.png)

- Processes 10 trillion events/day
- Online store: 800TB, serves 1TB/sec of reads
- Dual stores (offline Apache Iceberg + online KV store) to prevent train-serve skew

### Inference Architecture

![Inference architecture — feature collocation with local + distributed stores](../../images/20260524-1844-inference-bento-feedback.png)

**Two high-fanout strategies:**

1. **Feature collocation** — document features cached on inference instances; one user lookup then local memory reads during scoring
2. **Dedicated retrieval service** — ANN search + inverted indexes return pre-hydrated candidate sets

**Key optimization:** Serialization redesign allowing features to transfer as raw bytes (deserialization only inside engine) → 2x lower latency, 10x cheaper data plane.

### Feedback & Monitoring

- Every prediction + user action logged, flowing back into incremental training
- Statistical monitoring watches feature/prediction distributions for drift
- Online-offline comparison recomputes predictions using offline features
- Kubernetes-inspired reconciliation maintains desired vs. actual deployment state

### Scale

| Metric | Value |
|---|---|
| Predictions/sec | 1B+ |
| Latency budget | 100ms |
| Online feature data | 800TB |
| Feature read throughput | 1TB/sec |
| Models trained/day | Hundreds |
| Training compute hours/day | 100,000+ |
| Model size growth (2yr) | 20x |
| Training data growth (2yr) | 40x |

---

**Source:** https://blog.bytebytego.com/p/how-snapchat-serves-a-billion-predictions
**Date:** 2026-05-24
**Tags:** ml-inference, serving-infrastructure, feature-store, snapchat, gpu, ranking
