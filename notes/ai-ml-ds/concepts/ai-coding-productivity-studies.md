# AI Coding Productivity — Research Synthesis

Five 2025–2026 studies (synthesized by Brian Houck, DX) that converge on one finding: **AI compresses the upstream work of *writing* code and shifts the bottleneck downstream to review, verification, and delivery.**

## Key Takeaways

- The core problem: teams are *"generating code faster than we're generating the systems needed to safely understand, verify, and deliver it."*
- Raw coding-speed gains are large but **attenuate sharply** by the time they reach shipped software (+140–180% commits → only ~+30% releases)
- **DevEx and productivity are decoupling** — you can feel more productive while developer experience quietly degrades
- The durable new cost isn't in code (technical debt) but in **people and artifacts** — cognitive and intent debt
- Implication: move AI investment toward **verification** (review, testing, incident triage) and treat **understanding as a first-class deliverable**

## The Five Studies

### 1. Copilot dose-response (Heilman, Kyllo, Murphy-Hill)
Within-engineer study, 43 weeks, 16,223 developers. Highest-usage weeks → **~40% more completed PRs per hour** of coding time. Dose-response rises with usage depth (+21% low → +39% moderate → +40.5% high), leveling off at the top; held across 7 robustness tests; strongest on **larger PRs (7+ files)**.

![Copilot usage-depth dose-response: % change in PRs vs zero usage](../images/20260719-1510-copilot-dose-response.png)

### 2. Writing code vs. shipping code (Demirer, Musolff, Yang — NBER)
100,000+ GitHub developers. Commit increases of **+40%** (autocomplete), **+140%** (interactive agents), **+180%** (autonomous agents) — but the downstream effect tops out at **~+30% more releases**. Low elasticity of substitution (**~0.25**) → AI **complements**, not substitutes, human effort.

![Gains attenuating down the delivery pipeline](../images/20260719-1511-demirer-delivery-attenuation.png)

### 3. The productivity-experience paradox (Vella, Blincoe)
Longitudinal, 6 months, 95 professional engineers. **84%** reported productivity gains — yet those reporting *worse* DevEx on ≥1 dimension nearly **doubled (14% → 27%)**. Flow state was most vulnerable; productivity and DevEx change scores didn't correlate.

![The DevEx / productivity paradox](../images/20260719-1512-devex-productivity-paradox.png)

### 4. AI where it matters (Choudhuri, Bird, DeLine, Houck et al.)
Survey of **860 Microsoft developers**; catalogs **22 desired AI tools**. Introduces the **"right-shift" burden** (more generated code floods reviewers), the shift toward **verification**, the practice of **"bounded delegation"** (AI absorbs tedious assembly; humans keep core logic/architecture), and **four guardrails**: authority scoping (no auto-approvals), data provenance, uncertainty signaling, least-privilege access.

### 5. Technical → cognitive → intent debt (Storey, ACM Queue — Houck's top pick)
Three kinds of debt: **technical** lives in code, **cognitive** in people (*"an accumulation of not knowing"*), **intent** in artifacts (unclear goals/rationale). They compound (intent → cognitive → technical), and AI accelerates all three. Recommendation: treat **understanding as a deliverable**, not a byproduct.

![Triple-debt model: technical / cognitive / intent debt, accelerated by AI](../images/20260719-1513-storey-three-debts.png)

## See Also

- [ai-engineering-discipline.md](ai-engineering-discipline.md) — the practitioner's version: craft moves from writing code to encoding knowledge (observability, evals, verification)
- [../../leadership/codebase-drag-and-engineering-slowness.md](../../leadership/codebase-drag-and-engineering-slowness.md) — cites the METR "19% slower with AI" study
- [../../leadership/metrics-strategy-for-leaders.md](../../leadership/metrics-strategy-for-leaders.md) — why more metrics can mean worse detection

---

**Source:** https://newsletter.getdx.com/p/five-studies-that-are-changing-how
**Date:** 2026-07-19
**Tags:** ai-coding-assistants, developer-productivity, developer-experience, engineering-metrics, technical-debt, cognitive-debt, intent-debt, copilot, verification, research-synthesis
