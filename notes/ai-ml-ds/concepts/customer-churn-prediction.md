# Customer Churn Prediction

## Key Takeaways

- Traditional RFM (Recency-Frequency-Monetary) models are retrospective — they fail for new users, community platforms without transactions, and early-stage products with no purchase history
- Text reviews contain signals beyond star ratings: removing NLP analysis from a combined model drops accuracy by **37%**
- Users who add a review title are **11x more likely to remain loyal** than churning users — a single structural behavior predicts retention
- Transformer embeddings (BERT, RoBERTa, GPT-2) + structured metadata fused via TabTransformer reaches **92% loyalty prediction accuracy**
- SHAP token-level attribution surfaces specific vocabulary driving retention vs. churn, enabling targeted intervention

## The Problem with Classic Churn Models

RFM and similar purchase-history models:
- Require transaction logs → useless for new customers or platforms without commerce
- Are backward-looking → detect churn after it happens, not before
- Miss qualitative signals → written feedback contains intent that numbers don't

## Deep Learning Pipeline (2026 Research)

**Paper:** "Behavioral Loyalty Prediction from Customer Reviews in E-Commerce" — Pedram Kazemi Esfe & Fakhroddin Noorbehbahani, *Journal of Computing and Security*, Vol. 13, No. 1, June 2026.

### Architecture

| Stage | What it does |
|---|---|
| **Transformer embeddings** | BERT / RoBERTa / ALBERT / GPT-2 / Qwen encode review text into semantic vectors |
| **Structured processing** | Review scores and metadata processed in parallel |
| **TabTransformer fusion** | Combines text embeddings + categorical features via CNNs to prevent text from drowning structured signals |
| **Genetic algorithm calibration** | Evolves network topology across 19,440 possible configurations over 50 generations |

### Key Findings

- **92% prediction accuracy** on 58,000+ users
- Text-only model performs comparably to star-rating-only model — open-ended text encodes as much signal as explicit ratings
- Removing text analysis: **−37% accuracy**
- Presence of a review title: proxy for user investment, **11× loyalty lift**

### Explainability

SHAP values applied at token level: maps which specific words drive retention (e.g., "excellent") vs. churn (e.g., "delay"). Enables product teams to monitor vocabulary trends in reviews as a leading indicator.

## Practical Implications

- For products with text feedback but no transaction data: transformer-based review analysis can substitute for RFM
- Structural review behaviors (title added, review length, rating without text) are cheap features with high signal
- SHAP-based vocabulary monitoring can be built as a lightweight dashboard on top of any review corpus

---

**Source:** https://aiagentssimplified.substack.com/p/stop-guessing-why-users-leave-your
**Date:** 2026-06-25
**Tags:** churn-prediction, customer-retention, nlp, transformers, tabTransformer, shap, behavioral-analytics, loyalty, ml-pipeline, e-commerce
