# Lyft's LyftLearn ML Serving Platform

How Lyft built an ML serving platform on a **microservices-per-team** model with config-generator scaffolding — serving hundreds of millions of predictions daily for 40+ teams.

## Key Takeaways

- **Per-team microservices, not a shared platform.** Each ML team gets its own LyftLearn Serving microservice — generated from a scaffold — for independent deploys and library choices
- **Config-driven onboarding via Yeoman scaffolding.** An ML engineer answers a handful of questions; out comes a production-ready repo with Kubernetes configs, CI/CD, monitoring
- **Model self-tests** are first-class — ML engineers define expected outputs for test data; automated testing validates models during loads and PRs
- **Dual interfaces**: code-based repos for engineers; web UI for data scientists. Different surface for different consumer
- **40+ teams, hundreds of millions of predictions/day** since March 2022. Migrated off a monolithic legacy serving system

## The Per-Team Microservice Decision

Most ML platforms are designed as a single shared service hosting all models. Lyft went the other way: **one serving microservice per ML team**.

Why per-team rather than shared:
- **Independent deploys** — a marketing team's model deploy doesn't risk a pricing team's model
- **Independent library choice** — TensorFlow team vs PyTorch team vs LightGBM team all coexist
- **Independent scaling** — hot models scale without affecting cold ones
- **Blast radius** — a bug in one team's service doesn't crash everyone's

The trade-off: more microservices to operate. Lyft accepts this and amortizes the cost with strong scaffolding (see next section).

This is a real-world counterexample to "extract microservices reluctantly" ([microservices.md](../../system-design/microservices.md)) — for ML serving specifically, the per-team isolation pays off enough that the operational tax is worth it.

## Runtime Stack

```
   incoming request
        ↓
   Envoy (load balancer)
        ↓
   Gunicorn (Python WSGI)
        ↓
   Flask (HTTP layer)
        ↓
   Core library (model loading, versioning, inference, monitoring)
        ↓
   Custom code layer (per-team load() and predict() functions)
        ↓
   ML library (TF / PyTorch / LightGBM / XGBoost)
```

Each layer's responsibility is fixed; teams customize only the custom code layer and the model. Everything else (HTTP, observability, deploys) is shared infrastructure.

## Config-Generator Scaffolding (Yeoman)

The key operational lever. An ML engineer runs the generator:

```
$ yarn create lyftlearn-serving
? Service name: ml-pricing-driver-eta
? Team owner: pricing-team
? Model framework: tensorflow
? GPU? no
? ...
```

Output: a **production-ready repo** with:
- Kubernetes deployment manifests
- CI/CD pipeline (test → build → deploy)
- Monitoring dashboards
- Documentation templates
- Boilerplate `load_model()` and `predict()` stubs

Teams customize the model logic; everything else is generated and updateable.

**Why this works**: the scaffolding aggregates platform best practices — teams don't reinvent K8s configs, alerting rules, deploy pipelines. Updates to the scaffold propagate to all generated services as PRs.

This is **internal-tooling-as-product**, the same pattern as [Wise's microservice chassis](../../system-design/case-studies/wise-tech-stack.md).

## Model Self-Tests

A first-class feature: ML engineers define test data with expected outputs in the repo. The system automatically:
- Runs the tests on every model load
- Runs the tests in CI on every PR
- Blocks deploys where outputs deviate beyond tolerance

This catches:
- Model regressions during retraining
- Library version mismatches that affect predictions
- Feature-engineering bugs that silently change outputs

Without self-tests, ML services break in subtle ways that don't trigger normal alerts (the service is up, returning 200s, just with wrong predictions).

## Dual Interfaces

Different consumers need different surfaces:

| Consumer | Interface | Why |
|---|---|---|
| **ML engineers** | Git repo + code | Need full control, version history, code review |
| **Data scientists** | Web UI | Need to deploy notebook-developed models without touching infra |

The dual approach prevents the typical pattern where data scientists can't deploy without engineering support, creating a bottleneck.

## What This Solves (and Doesn't)

**Strong fit:**
- Multi-team ML org where each team has 1+ models in production
- Diverse model frameworks (some TF, some PyTorch, etc.)
- Need for independent team deploys
- Hundreds of millions of predictions/day across many models

**Bad fit:**
- Single-team ML — overhead of per-team services doesn't pay
- One giant model serving most predictions — shared serving is simpler
- Pre-production / R&D — too much infra for prototyping

**What it doesn't solve:**
- The training pipeline (a separate concern at Lyft)
- The feature store (typically separate from serving)
- Model governance / lifecycle management (separate tooling)

LyftLearn Serving is specifically the inference-time piece.

## Generalizable Patterns

1. **Per-team isolation when teams have diverging needs.** Don't force one platform on everyone if the operational overhead of separation is justified
2. **Scaffolding > documentation.** A generator that produces a working service is more useful than docs telling you how to build one
3. **First-class model tests.** ML services can be "up" while returning wrong predictions — explicit test data with expected outputs catches this
4. **Different interfaces for different consumers.** Web UI for data scientists, code for engineers — same backend
5. **Microservices when justified, not by default.** Lyft chose microservices for ML serving with their eyes open about the operational cost

## Related

- [ML systems at scale](ml-systems-at-scale.md) — broader ML systems patterns; LyftLearn is one production case
- [Stripe fraud detection](../../system-design/security/stripe-fraud-detection.md) — adjacent ML serving case at scale
- [Microservices](../../system-design/microservices.md) — when microservices make sense (Lyft is a "yes" case)
- [Wise tech stack](../../system-design/case-studies/wise-tech-stack.md) — microservice chassis pattern (similar scaffolding approach)
- [CPU vs GPU vs TPU](cpu-gpu-tpu.md) — hardware substrate

---

**Source:** https://blog.bytebytego.com/p/how-lyft-built-an-ml-platform-that
**Date:** 2026-06-05
**Tags:** lyft, lyftlearn, ml-serving, microservices, yeoman, scaffolding, model-self-tests, ml-platform, case-study
