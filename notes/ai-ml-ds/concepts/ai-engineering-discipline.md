# AI Engineering Discipline

## Key Takeaways
- The economics of code production inverted in 2025: code went from expensive and precious to free and disposable — the same shift as handcrafted server pets → immutable cattle, now extended to application code
- Code should be treated as a **cache** of shared understanding — a materialized view useful while current, disposable when stale — not a durable artifact worth gatekeeping
- Human brains are the weakest link in validation (nitpicking, repetition, small-detail catching); machines should own quality gates; engineers should own creativity, architecture, and encoding knowledge into the system
- Nondeterministic AI-generated systems require *more* engineering discipline — observability, behavioral tests, production-stage evals — not less
- 2026 is a return to discipline: the returns on encoding system knowledge (specs, traces, architecture artifacts) are now massive and nonlinear because every organization wants AI leverage

## The Core Economic Shift

For most of computing history, lines of code were the scarce, precious artifact: hard to produce, maintained carefully, accumulated meaning over time. Rewrites were expensive. Re-validation was risky. Code was the bundled repository of developer intent, user expectations, edge-case history.

That changed in 2025. Code went from scarce to **effectively free and instant**.

The implication: code is no longer an asset — it's a cache.

> "When regeneration is easy, code stops being an asset and starts acting as a cache: a materialized view of understanding that is useful while current, disposable when stale." — Chad Fowler

## The Phoenix Architecture Mental Model

Chad Fowler (who coined "immutable infrastructure" in 2013) extended the immutable-infra premise to application code:

| Era | What changed? | Premise |
|---|---|---|
| Handcrafted servers → cattle | Infrastructure | Never fix a running thing — replace it |
| Precious code → regenerable code | Application | Never edit in place — replace it |

Both transitions share the same driver: once regeneration becomes cheap, **mutation accumulates entropy**. Replacement resets it. Editing in place creates drift.

Key essays: "The Death and Rebirth of Programming," "The Deletion Test," "Relocating Rigor."

## The Deletion Test

Apply to any system you own:

> Imagine deleting the entire implementation. If the team panics, the real problem is not the code — it's an **evaluation problem.**

What the panic reveals:
- We don't know exactly what behavior is required
- We don't know which failures are unacceptable
- We don't know what invariants must always hold
- We don't know how to tell if a new version is correct
- We don't know which bugs are intentional fixes for forgotten edge cases

These are not code problems. They are knowledge-encoding problems.

**Code becomes precious only when it is the only place knowledge lives.** The solution is encoding that knowledge elsewhere: specs, behavioral tests, observability, architecture artifacts.

## Code Was Doing Too Much

Lines of code have been the bundled, opaque container for:
- Developer intent
- User expectations (implicit and explicit)
- Bug history and edge-case fixes
- Architecture decisions (inferred, not explicit)

None of these are inspectable or reviewable at the line-of-code level. The result: painful migrations, strangler-fig rewrites that take years, architecture you can only guess at from the code.

The alternative: **architecture artifacts are the primary reviewed artifact**; code is regenerated from changes to the architecture.

The tools to do this fully don't exist yet, but the ideas do — from operations and QA:
- Behavioral tests
- Characterization tests
- Capture/replay
- Traffic splitters
- Observability (the real kind: traces, not logs)

## Relocating Validation to Machines

Human brains are poor at validation:
- Forgetful and distractible
- Habituated to repetition (miss the same pattern twice)
- Bad at spotting small details
- The model in our heads perpetually diverges from what users actually experience

The argument: humans should not be the primary quality gate for AI-generated code. Machines beat us at validation. **Human advantages** — creativity, leaps of logic, encoding system knowledge — belong elsewhere.

Required disciplines for the AI-code era:
1. Instrumentation with traces (observability first)
2. Tests and evals that run in production (not just CI)
3. Behavioral/characterization tests (encode what *is*, not just what *ought* to be)
4. Capture/replay and traffic splitters
5. Architecture artifacts as the primary reviewed artifact

> "Production is not what happens after development is over. **Production is a stage of development.**"

## 2026: Return to Discipline

2025 was the "vibe coding" year — AI got as good at generating code as the median software engineer. The range of possible futures felt impossibly wide open.

2026 is shaping up to be a return to engineering discipline:

- The knowledge in engineers' heads is **unavailable to AI until encoded into the system** — specs, tests, traces, architecture
- The returns on those investments are now massive and nonlinear (CEO demand for AI leverage is real)
- Short-feedback-loop teams (the cardinal sign of disciplined engineering) are estimated at under 5-10% of teams — AI tooling brings this within reach for far more teams
- Determinism is not going away: people don't want buttons that randomly move; financial transactions must complete reliably

> "AI is not magic. This is still engineering. Technology needs technologists."

The shift isn't that engineers become unnecessary. It's that engineers' highest-value work moves from manually writing and gatekeeping lines of code to **encoding knowledge into systems** — through observability, behavioral tests, and architecture artifacts that can guide regeneration.

---

**Source:** https://charitydotwtf.substack.com/p/ai-demands-more-engineering-discipline
**Date:** 2026-06-18
**Tags:** ai-engineering, engineering-discipline, immutable-infrastructure, phoenix-architecture, code-as-cache, observability, validation, nondeterministic-systems, vibe-coding, production-engineering, chad-fowler, charity-majors
