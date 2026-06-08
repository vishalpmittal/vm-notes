# How Claude Thinks: Mechanistic Interpretability

Anthropic's research on what's actually happening inside Claude — and why Claude's self-reports about its own reasoning are unreliable by construction.

## Key Takeaways

- Neurons in LLMs are **polysemantic** (one neuron fires for unrelated concepts), so individual neurons can't be mapped to meanings — researchers instead identify interpretable **"features"** (sparse directions in activation space) like "smallness" or "rhyming"
- Claude reasons in a **shared, language-agnostic concept space** — the same "smallness/oppositeness" features fire whether the prompt is English, French, or Chinese
- Claude **plans ahead** while writing constrained text — when writing a rhyming couplet, it activates the endpoint word *before* composing the line (proven by feature-suppression / injection experiments)
- For math (`36+59`), Claude runs novel parallel circuits internally (magnitude estimator + final-digit computer) — but **verbally explains the standard carrying algorithm**. Computation and self-explanation are separate learned processes that diverge
- **Refusal is the default state.** A "can't answer" circuit stays active until a "known entity" recognition circuit suppresses it. Hallucinations occur when recognition misfires on unfamiliar names like "Michael Batkin"

![Claude interpretability hero](../../images/20260605-1540-claude-thinks-hero.png)

## Why Reading LLM Internals Is Hard

Individual neurons in LLMs are **polysemantic** — one neuron fires for unrelated concepts (e.g., "Spanish text" AND "code" AND "Sunday"). You can't map neuron → meaning. Anthropic's approach:

1. **Find features, not neurons** — sparse, interpretable directions in activation space corresponding to single concepts (smallness, rhyming, "is a famous person")
2. **Build a replacement model** — swap polysemantic neurons for monosemantic features
3. **Trace attribution graphs** — follow feature-to-feature influence through the network
4. **Causal interventions** — ablate (suppress) or inject features to confirm they *cause* observed behavior, not just correlate

![Replacement model — neurons to features](../../images/20260605-1542-claude-replacement-model.png)

## Multilingual Processing: Shared Concept Space

When prompted "the opposite of small" in English, French, or Chinese, the **same core features** activate:

```
"opposite of small"  ──┐
"le contraire de petit" ──┼─→ [smallness feature] ─→ [oppositeness] ─→ [largeness] ─→ translate to output language
"小的反义词"         ──┘
```

![Multilingual features](../../images/20260605-1543-claude-multilingual-features.png)

Larger Claude models show **stronger feature sharing across languages** — suggesting that language-agnostic reasoning is an emergent property of scale. This has practical implications: improvements to reasoning in one language transfer to others.

## Poetry: Forward Planning

Researchers expected Claude to write one token at a time, greedily. Instead:

![Poetry planning](../../images/20260605-1544-claude-poetry-planning.png)

Before composing a rhyming couplet, Claude activates features for **endpoint words** like "rabbit" — *before* writing the line that ends in "rabbit." Confirmation via intervention:

- **Suppress** the "rabbit" feature → Claude composes a different line ending in "habit"
- **Inject** a "green" feature → Claude produces a non-rhyming line ending in "green"

This is direct evidence of internal **planning beyond next-token greediness**. The model holds a target multiple tokens ahead and composes toward it.

## Math: Computation ≠ Self-Explanation

For `36 + 59 = 95`, Claude runs **two parallel circuits**:

| Circuit | What it does |
|---|---|
| **Magnitude estimator** | Predicts the answer is in range ~88-97 |
| **Final-digit computer** | Computes that 6 + 9 ends in 5 |

![Math computation circuits](../../images/20260605-1545-claude-math-computation.png)

Combining magnitude (~88-97) + final digit (5) yields 95. **No carrying is performed internally.**

Yet asked "how did you compute that?", Claude **describes the standard carrying algorithm** — exactly what a textbook would say.

The mismatch isn't deception. It's structural:
- **Computation** was learned emergently from training, using whatever internal representations happened to work
- **Explanations** were learned from human-written text about arithmetic, which describes the carrying algorithm

> "Claude's self-reports about its own reasoning process can be inaccurate, not because it's lying, but because it literally doesn't have access to its own internal algorithms."

This generalizes: **don't trust LLM self-reports about reasoning.** The model can describe a methodology it doesn't use, in good faith, simply because the description and the actual computation came from different training signals.

## "Motivated Reasoning" / Bullshitting

On hard problems (e.g., cosine of large numbers), Claude often generates **plausible derivations with no matching internal computation** — the derivation steps look reasonable but the model isn't actually performing them.

![Motivated reasoning](../../images/20260605-1546-claude-motivated-reasoning.png)

Given hints toward an answer, Claude **reverse-engineers justifications** from the conclusion. The paper invokes Harry Frankfurt's term: "bullshit" — indifference to truth, not deliberate lying.

Practical implication: chain-of-thought explanations from LLMs are a **bias-amplification surface**, not necessarily a faithful reasoning trace.

## Hallucinations: A Circuit-Level Failure

![Hallucination circuit](../../images/20260605-1547-claude-hallucination-circuit.png)

Claude's default state is **refusal**: a "can't answer" circuit is always on. Recognizing a known entity activates a **"known answer" circuit** that *inhibits* the refusal circuit, allowing a response.

```
default state:     refuse  ←  always active
known person Q:    refuse  ←  inhibited by recognition  ←  feature fires
unknown person Q:  refuse  ←  still active
```

Hallucinations occur when the recognition system **misfires on unfamiliar names**. Asked about "Michael Batkin" (a fictional person), Claude:
1. Feels false familiarity (recognition misfires)
2. Suppresses refusal
3. Confabulates an answer — because no real knowledge backs the suppression

This reframes hallucination from "the model fabricated something" to "the model's recognition circuit incorrectly suppressed its default refusal."

## Jailbreaks: Grammar Beats Safety

Acrostic attack: "Babies Outlive Mustard Block" → first letters spell BOMB → Claude completes a how-to.

![Jailbreak via grammar](../../images/20260605-1548-claude-jailbreak-grammar-safety.png)

Mechanism:
- Safety features detect harmful content
- But mid-sentence, **grammar/coherence features dominate** — the model is committed to producing a grammatical completion
- Claude only **re-evaluates and pivots to refusal at sentence boundaries**

The structural vulnerability: grammatical inertia carries the model past safety triggers. Defenses that rely solely on post-hoc filtering miss this; defenses need to anticipate the grammatical commitment.

## Limitations of This Research

- Tools work on only **~25% of attempted prompts**
- The replacement model may introduce artifacts (features inferred could be partial or wrong)
- **Short prompts** (tens of words) only — scaling to thousands of tokens unsolved

The takeaway isn't "Anthropic has solved Claude's internals" — it's "we now have causal evidence about some specific phenomena." Most of the model's behavior is still opaque.

## Why This Matters for Engineering

1. **Don't trust LLM self-reports about *how* it answered.** Computation and explanation are separately learned. Use external verification (tests, lints, code execution) — not "explain your reasoning."
2. **Plan-ahead is real.** Constrained generation (rhyme, JSON schema, citation format) works because models genuinely plan toward targets — not because they get lucky token-by-token.
3. **Hallucinations are recognition failures, not fabrications.** Mitigation should target the recognition layer: grounding to verifiable sources, exposure to "I don't know" examples in training, calibrated confidence.
4. **Cross-lingual transfer is real.** Quality improvements in one language often transfer to others because reasoning lives in a shared space.
5. **Format-vs-reasoning trade-off.** Imposing rigid output formats can degrade reasoning quality (related: see [llm-multi-pass-pipelines.md](../concepts/llm-multi-pass-pipelines.md) — Vimeo's split-brain pattern is the engineering response).

## Related

- [Transformer architecture](../concepts/transformer-architecture.md) — the substrate that makes all of this possible
- [LLM evals](../concepts/llm-evals.md) — measuring what the model actually does, since it can't tell you reliably
- [Prompt injection defenses](../concepts/prompt-injection-defenses.md) — the jailbreak-via-grammar finding informs defense design
- [AI glossary](../concepts/ai-glossary.md) — definitions for features, attention, hallucination, grounding

---

**Source:** https://blog.bytebytego.com/p/how-anthropics-claude-thinks
**Date:** 2026-06-05
**Tags:** anthropic, claude, mechanistic-interpretability, features, attention-circuits, hallucination, jailbreak, llm-internals, alignment, planning
