# ML System Design Interview

## Key Takeaways

- Production ML systems are end-to-end pipelines (data → features → training → serving), not isolated algorithms — interview answers should reflect this scope
- Feature quality usually matters more than model sophistication; feature engineering is the highest-leverage step
- Label definition is a design decision — "did the user enjoy this movie?" can be operationalized as completion rate, ratings, or next-watched content, and each choice produces a different model
- Class imbalance and data leakage are the silent killers — first-try scores >95% almost always indicate leakage
- Reproducibility requires data versioning (DVC, MLflow) alongside code versioning; the dataset is part of the model artifact

## Part 1: Raw Material — How Data Becomes Intelligence

### Features

Measurable signals the model consumes — e.g., user age, time of day, device, prior ad exposure for an ad-click predictor. **Quality of features outweighs model complexity** in most production systems.

### Feature Engineering

Transforming raw data into useful signals. Uber example: a raw timestamp becomes day-of-week, time-category (morning rush), holiday flag, weather conditions — turning one column into several high-signal inputs.

### Labels and Ground Truth

- **Label** = the target the model predicts
- **Ground truth** = what actually happened

Operationalizing the label is a modeling decision. Netflix's "Did the user enjoy this movie?" can be expressed as completion rate, explicit rating, or next-watched content. Each choice trains a different model.

### Training, Validation, and Test Sets

- Common splits: 70/15/15 or 80/10/10
- For non-stationary data (time series, recsys), use **time-based splits** to avoid future-leakage
- **Validation** guides mid-training selection (hyperparameters, model choice)
- **Test** is final-only — touching it during development invalidates it

### Class Imbalance and Data Leakage

- **Imbalance** (e.g., 1-in-10,000 fraud) requires sampling, weighting, or specialized loss functions
- **Leakage** = training data contains information unavailable at inference time
- **Heuristic:** first-try accuracy >95% → audit features for leakage

### Data Pipelines and ETL

Extract-Transform-Load is the "invisible plumbing" that converts raw data into model-ready inputs. Silent failures (dropped rows, nulls, schema drift) silently degrade predictions and are hard to debug.

### Data Versioning

- **DVC**, **MLflow** track which dataset trained which model
- A reproducible model = code version + data version + hyperparameters
- Without data versioning, debugging a regression months later is guesswork

## Part 2: Pattern Machines — How Models Learn

### Model Training and Loss Functions

Training = minimize a loss function over the training set.

| Problem type | Loss function |
|---|---|
| Regression (predict a number) | MSE (mean squared error) |
| Binary / multi-class classification | Cross-entropy |
| Ranking | Pairwise / listwise losses |

Loss choice is driven by problem shape, not preference.

### Parameters and Hyperparameters

- **Parameters** = learned during training (weights, biases) — GPT-4 has billions
- **Hyperparameters** = set *before* training: learning rate, batch size, epochs, architecture choices, regularization strength

### Embeddings

Numerical vector representations capturing semantic meaning — related concepts cluster in vector space.

- **Spotify** uses song embeddings for similar-song recommendations
- **Netflix** uses both movie and user embeddings; recommendations come from cosine similarity in shared space

See also: [vector-databases.md](vector-databases.md) for how embeddings get served at scale.

## Interview-Ready Vocabulary

| Term | One-line definition |
|---|---|
| Feature | Measurable signal the model consumes |
| Label / ground truth | Prediction target / what actually happened |
| Leakage | Train-time features unavailable at inference |
| Class imbalance | Skewed target distribution requiring sampling/weighting |
| ETL | Extract-Transform-Load — the data pipeline |
| Loss function | What training minimizes |
| Hyperparameter | Knob set before training (vs parameter learned during) |
| Embedding | Vector representation of semantic meaning |
| Data versioning | Tracking dataset version alongside code/model version |

> **Note:** This article was paywalled past Part 2. Topics like overfitting, regularization, evaluation metrics, A/B testing, and serving architecture were not captured here. See [ml-systems-at-scale.md](../inference/ml-systems-at-scale.md) for production-serving content.

---

**Source:** https://newsletter.systemdesign.one/p/machine-learning-system-design-interview
**Date:** 2026-06-01
**Tags:** machine-learning, system-design, ml-interview, feature-engineering, embeddings, mlops, data-pipelines, data-versioning
