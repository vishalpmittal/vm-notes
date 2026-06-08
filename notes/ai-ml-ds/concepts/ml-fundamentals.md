# ML Fundamentals Glossary

The traditional ML/data-science terminology — the layer beneath the LLM-era concepts in [ai-glossary.md](ai-glossary.md). AI → ML → DL hierarchy, supervised vs unsupervised, the canonical scikit-learn algorithm families.

## Key Takeaways

- **AI ⊃ ML ⊃ DL** — Artificial Intelligence is the umbrella; Machine Learning is the subset that uses statistical tools to learn from data; Deep Learning is the further subset using multi-layer neural networks
- The **supervised vs unsupervised** split is the first branching decision — labeled data → supervised (regression/classification); unlabeled data → unsupervised (clustering/dimensionality reduction)
- **Scikit-learn** is the canonical Python ML library; algorithms cluster into 6 families (Classification, Regression, Clustering, Dimensionality Reduction, Model Selection, Preprocessing)
- The **classical ML stack** (scikit-learn, pandas, numpy) handles most real-world tabular ML problems — deep learning frameworks (TensorFlow, PyTorch) come in for unstructured data (images, text, audio)
- Specialized AI concepts to know: **NLP** (text), **Reinforcement Learning** (reward-driven), **ANI vs AGI** (narrow vs general intelligence)

## The Hierarchy

```
AI (Artificial Intelligence)
 └─ ML (Machine Learning)
     └─ DL (Deep Learning)
```

### AI — Artificial Intelligence
The art of creating an application that can take decisions without human intervention. Examples:
- Netflix / Amazon / YouTube recommendations
- Self-driving cars

### ML — Machine Learning
Subset of AI that provides statistical tools to analyze and visualize data for **prediction and forecasting**. Branches into:
- Supervised ML
- Unsupervised ML

### DL — Deep Learning
Subset of ML aiming to mimic the human brain via **multi-layer neural networks**.

Frameworks:
- **TensorFlow** — open-source DL framework by Google
- **PyTorch** — open-source ML library
- **DL4J** — deep learning framework for Java

## Supervised ML

Training on a **labeled dataset** (each example has the right answer). The model learns to map inputs to known outputs.

**Examples:**
- Spam filtering
- Speech recognition
- Language translation
- Online adaptive advertising
- Self-driving car radar
- Visual inspections

**Two branches:**
- **Regression** — predict a continuous value (price, temperature, time)
  - Hypothesis Model — function mapping independent features → dependent feature
- **Classification** — predict a discrete category (spam/not-spam, dog/cat/bird)

### Hypothesis Model
- **Independent Feature** — input variable (X)
- **Dependent Feature** — output variable being predicted (Y)
- The model finds the function `Y = f(X)`

## Unsupervised ML

Training on **unlabeled data** — the model finds structure without being told what to look for.

**Two branches:**
- **Clustering** — group similar examples together (customer segments, image categories)
- **Dimensionality Reduction** — compress many features into fewer while preserving structure (PCA, t-SNE, UMAP)

## Reinforcement Learning

A third paradigm (orthogonal to supervised/unsupervised): an **agent** learns by interacting with an **environment**, receiving **rewards** for good actions and **penalties** for bad ones.

**Examples:**
- Game playing (AlphaGo, OpenAI Five)
- Robotics
- Recommendation systems (long-term engagement optimization)
- LLM alignment (RLHF — see [ai-glossary.md § evals](ai-glossary.md#21-evals) and modern variant RLVR in [ai-trends-2026.md](ai-trends-2026.md))

## Scikit-Learn Algorithm Families

Six categories cover most of scikit-learn's surface:

| Family | Common algorithms | Use case |
|---|---|---|
| **Classification** | Logistic Regression, Random Forest, SVM, K-Nearest Neighbors | Predict a discrete label |
| **Regression** | Linear Regression, Ridge / Lasso, Random Forest Regressor | Predict a continuous value |
| **Clustering** | K-Means, DBSCAN, Agglomerative Clustering | Group unlabeled data |
| **Dimensionality Reduction** | PCA (Principal Component Analysis) | Compress features while preserving structure |
| **Model Selection** | GridSearchCV, RandomizedSearchCV | Hyperparameter search |
| **Preprocessing** | StandardScaler, OneHotEncoder, Imputer | Prepare data before training |

### When to pick which

- **Tabular data, <1M rows** → start with classical ML (scikit-learn)
- **Tabular data, >>1M rows or non-linear** → gradient boosting (XGBoost, LightGBM, CatBoost)
- **Images, text, audio** → deep learning (PyTorch, TensorFlow)
- **Sequential decisions** → reinforcement learning
- **Foundation model prompts cheaper than training** → use an LLM, not training your own classifier

## Specialized Branches

### Neural Networks
Computational models inspired by the human brain's interconnected neuron structure. The building block of deep learning. Variants:
- **Feedforward NN** — basic, all forward connections
- **CNN (Convolutional)** — images, spatial structure
- **RNN / LSTM / GRU** — sequences, time series
- **Transformer** — sequences with attention; the modern default (see [transformer-architecture.md](transformer-architecture.md))

Open-source library: **OpenNN**.

### NLP — Natural Language Processing
Branch of AI focused on understanding and generating human language. Now dominated by transformer-based LLMs (see [transformer-architecture.md](transformer-architecture.md), [ai-glossary.md](ai-glossary.md)), but classical NLP techniques (tokenization, stemming, named entity recognition, sentiment analysis) still matter as preprocessing.

### ANI vs AGI
- **ANI — Artificial Narrow Intelligence** — AI that's superhuman at a specific task (chess engines, image classifiers, current LLMs in many domains). Everything we have today.
- **AGI — Artificial General Intelligence** — AI matching human-level performance across the *full range* of cognitive tasks. Hypothetical, contested timeline.

Some researchers add **ASI (Artificial Super Intelligence)** — AI exceeding human intelligence across all domains.

## How This Glossary Connects to the Rest of the Repo

This note covers the **classical ML era** (2000s-2020). For the **LLM era** (2020+) terminology, see:

- [ai-glossary.md](ai-glossary.md) — 25 LLM-era concepts: tokens, attention, RAG, agents, fine-tuning, LoRA, quantization, evals
- [transformer-architecture.md](transformer-architecture.md) — the architecture behind modern NLP
- [ai-engineering-fundamentals.md](ai-engineering-fundamentals.md) — Software 1.0/2.0/3.0 progression
- [ml-system-design-interview.md](ml-system-design-interview.md) — end-to-end ML pipeline design
- [genai-system-design.md](genai-system-design.md) — generative AI systems
- [multimodal-llms.md](multimodal-llms.md) — multimodal (text + image + audio + video)

## Related (Deeper Dives by Topic)

- [Vector databases](vector-databases.md) — embeddings + similarity search
- [Image generation: diffusion](image-generation-diffusion.md) — autoregressive vs diffusion image models
- [LLM tool use and MCP](llm-tool-use-and-mcp.md) — extending LLMs with tools
- [ML systems at scale](../inference/ml-systems-at-scale.md) — production ML serving
- [CPU vs GPU vs TPU](../inference/cpu-gpu-tpu.md) — hardware substrate

---

**Source:** Personal glossary CSV (VM - AI.csv) compiled by user
**Date:** 2026-06-05
**Tags:** ml, machine-learning, deep-learning, ai, fundamentals, glossary, supervised, unsupervised, scikit-learn, neural-networks, nlp, reinforcement-learning, ani, agi
