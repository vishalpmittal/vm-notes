# ML Foundations — Complete ML by Krish Naik

Notes from Krish Naik's "Complete Machine Learning in 6 Hours" YouTube course. Taxonomy of ML categories, core algorithms, and the bias/variance framing that explains overfitting.

## Key Takeaways

- ML splits into three paradigms: **Supervised** (labeled data → predict), **Unsupervised** (find structure), **Reinforcement** (learn via reward)
- Supervised further splits into **Regression** (continuous output) and **Classification** (discrete output) — same algorithms often work for both with tweaks
- The single most useful framing for diagnosing a model: **bias vs. variance** — overfitting = low bias + high variance, underfitting = high bias + high variance, generalized = low + low
- Linear Regression has assumptions that get skipped in practice and bite later: normality, standardized features, linearity, no multicollinearity (check via VIF)
- Logistic Regression is binary classification dressed up as regression — sigmoid maps to (0, 1), a decision boundary turns that into a class

## ML Paradigms

### Supervised ML

Train on labeled data: `(input features, known output) → learn the mapping`.

**Regression** — predict a continuous number

- **Hypothesis** — the model's predicted relationship between inputs and output
- **Independent feature** — input to the model (the X)
- **Dependent feature** — what's being predicted (the Y)

**Classification** — predict a discrete label

- Example: email/text spam classification (spam vs. not-spam)

**Algorithms** (cover both regression and classification, with variations):

- Linear Regression
- Ridge and Lasso (regularized linear regression)
- Logistic Regression (despite the name, classification)
- Decision Tree
- K-Means Clustering
- AdaBoost
- Random Forest
- Gradient Boosting
- XGBoost
- Naive Bayes
- SVM (Support Vector Machine)
- KNN (K-Nearest Neighbors)

### Unsupervised ML

No labels — find structure in unlabeled data.

**Clustering** — group similar objects together into buckets

**Dimensionality Reduction** — compress high-dimensional data into fewer dimensions while preserving structure
- PCA (Principal Component Analysis)
- LDA (Linear Discriminant Analysis)

**Algorithms:**
- K-Means
- DBSCAN (density-based clustering)
- Hierarchical clustering
- KNN (used for unsupervised proximity tasks too)
- PCA
- LDA

### Reinforcement ML

Agent learns through trial and error guided by reward signals. Outside the scope of this section but completes the taxonomy.

## Bias vs. Variance — The Diagnostic Frame

This is the single most useful way to think about why a model is failing.

| Condition | Train accuracy | Test accuracy | Label |
|---|---|---|---|
| Low bias, high variance | Good | Bad | **Overfitting** — memorized training data |
| High bias, high variance | Bad | Bad | **Underfitting** — model can't capture signal |
| Low bias, low variance | Good | Good | **Generalized** — what you want |

- **Bias** = how well the model fits the training data
- **Variance** = how well training fit generalizes to unseen data

The cure for overfitting and underfitting are opposite — diagnosing which one you have before changing the model is the difference between solving the problem and making it worse.

## Linear Regression — Assumptions

Linear regression is often "just import sklearn and fit," but the model assumes:

- **Normal / Gaussian distribution** — residuals should be normally distributed
- **Standardization** — features on comparable scales (mean 0, std 1)
- **Linearity** — the actual relationship between X and Y is linear
- **No multicollinearity** — input features aren't highly correlated with each other
- **Variance Inflation Factor (VIF)** — quantitative check for multicollinearity (rule of thumb: VIF > 5 or 10 is a problem)

Violating these assumptions doesn't always break the model, but it does invalidate the statistical interpretations (p-values, confidence intervals) and often degrades predictive performance.

## Logistic Regression

- Works very well with **binary classification** (also extends to multinomial)
- Output is squashed through a **sigmoid function** so it maps to a probability in (0, 1)
- A **decision boundary** (typically threshold = 0.5) turns the probability into a discrete class label
- Despite the name, it's a classification algorithm — the "regression" refers to the underlying linear combination of features, not the output type

## Related Notes

- [ML fundamentals (repo concept note)](../../ai-ml-ds/concepts/ml-fundamentals.md) — deeper, framework-agnostic treatment
- [Transformer architecture](../../ai-ml-ds/concepts/transformer-architecture.md) — the modern continuation of the supervised-ML lineage
- [AI glossary](../../ai-ml-ds/concepts/ai-glossary.md) — quick lookups for terms used here

---

**Source:** https://youtu.be/JxgmHe2NyeY (Krish Naik — Complete Machine Learning In 6 Hours)
**Source:** /Users/vimittal/Downloads/Complete ML By KrishN.html (course notes)
**Date:** 2026-06-17
**Tags:** machine-learning, supervised, unsupervised, reinforcement, regression, classification, clustering, dimensionality-reduction, bias-variance, overfitting, underfitting, linear-regression, logistic-regression, sigmoid, course-notes, krish-naik
