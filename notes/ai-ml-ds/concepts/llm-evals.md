# LLM Evals

## Key Takeaways

- LLMs break traditional testing: non-deterministic outputs, fuzzy correctness, and silent regressions mean assert-based tests are insufficient
- LLM-as-judge is the scalable scoring method — use a capable model to apply rubrics, but calibrate against human judgment before trusting it
- Golden sets must be sampled from real production traffic, not just anticipated scenarios — coverage gap between "examples you wrote" and "real traffic" is where failures hide
- RAG systems need the triad eval: faithfulness (grounded in context?), answer relevance (addresses the question?), context precision (retrieval quality?)
- Stack your eval layers: heuristics → semantic similarity → LLM-as-judge (offline) → LLM-as-judge (online) → human spot checks

## Why LLMs Break Traditional Testing

![Deterministic vs non-deterministic systems](../../images/20260528-2300-llm-nondeterminism.png)

- **Non-determinism** — same prompt produces different outputs across runs; breaks "same input → same output" testing assumption
- **Fuzzy correctness** — rarely one correct answer; instead a range of acceptable responses across multiple quality dimensions
- **Silent regression** — without systematic eval, prompt changes are blind bets

## Evaluation Primitives

![Golden set pipeline — from production traffic to deployment](../../images/20260528-2301-golden-set-pipeline.png)

- **Criteria** — product-level dimensions defining "good" for your use case
- **Rubric** — operationalized criteria creating specific, scorable questions for reproducible evaluation
- **Test cases** — input/output pairs; ideally sampled from production traffic
- **Golden set** — curated high-quality test cases representing real user behavior
- **Pass/fail threshold** — score boundary converting continuous ratings into deploy/no-deploy decisions

![Eval coverage gap — anticipated vs real traffic](../../images/20260528-2302-eval-coverage-gap.png)

**Eval coverage** — how well your golden set reflects actual user inputs. Low coverage = optimistically misleading results.

## Scoring Methods

![LLM-as-judge — input, output, rubric → score + explanation](../../images/20260528-2303-llm-as-judge.png)

| Method | Speed | Cost | Catches |
|---|---|---|---|
| Heuristic/code-based | Fast | Cheap | Format, length, banned phrases |
| Semantic similarity | Fast | Cheap | Meaning drift from reference |
| Task-specific (BLEU, ROUGE) | Fast | Cheap | Surface-level translation/summary quality |
| LLM-as-judge | Medium | Medium | Quality dimensions via rubric at scale |
| Human evaluation | Slow | Expensive | Gold standard; use for calibration and debugging |

**Pointwise vs pairwise** — pointwise scores individual outputs; pairwise compares two. Pairwise is more reliable but costlier.

![Judge calibration — ungrounded vs calibrated judge aligned with human scores](../../images/20260528-2304-judge-calibration.jpeg)

**Judge calibration** — validate that LLM judges match human judgment through agreement analysis before deploying them.

## RAG Evaluation

![RAG triad — faithfulness, answer relevance, context precision](../../images/20260528-2305-rag-triad.jpeg)

![RAG eval pipeline — retrieve, augment, generate with quality checks](../../images/20260528-2306-rag-eval-pipeline.png)

Three failure dimensions:

- **Faithfulness** — is the answer grounded in retrieved context?
- **Answer relevance** — does it address the user's question?
- **Context precision** — did retrieval fetch the right documents?

Failure patterns: irrelevant retrieval, model ignoring retrieved context, or grounded-but-unhelpful answers.

## Offline vs Online Eval

![Offline eval (pre-deploy, golden set) vs online eval (production monitoring)](../../images/20260528-2307-offline-vs-online-eval.png)

- **Offline** — pre-deployment testing using best judge models on golden datasets; acts as CI pipeline for LLMs
- **Online** — production monitoring using cheaper heuristics and smaller models on live outputs
- **Prompt versioning** — track prompt changes via version control; run regression tests per version

## Anti-Patterns

- Vibe-based evaluation through informal spot-checking
- Single-sample evaluation ignoring nondeterminism
- Goodhart's Law: optimizing metrics until they stop measuring quality
- Golden set misaligned with real user behavior
- Ignoring tail failures in favor of averages

## MVP Eval Stack

1. Build 50-example golden set mixing real queries, edge cases, and adversarial inputs
2. Add one deterministic heuristic for the most critical structural requirement
3. Create one LLM judge prompt targeting a key quality dimension

---

**Source:** https://newsletter.systemdesign.one/p/llm-evals
**Date:** 2026-05-28
**Tags:** evals, llm-as-judge, golden-set, rag, benchmarks, testing, non-determinism
