# Manager 30 / 60 / 90 Day Plan

The default onboarding framework for a new engineering management role. The plan is **phased by intent** — learn → assess → align → execute — not by task list. Avoid the temptation to ship visible wins in week 1; the org will judge you on the right calls in month 3, not the splashy ones in week 1.

## Key Takeaways

- **Days 1–30 = learn + assess.** Resist the urge to change anything. The cost of a wrong early call is much higher than the cost of moving slow
- **Days 31–60 = align + prioritize.** Now you have enough context to commit to direction
- **Days 61–90 = execute + institutionalize.** Ship 1–2 visible wins *and* set the systems that outlive you
- **The single most useful sentence to share at Day 30:** *"What I will not change yet"* — explicit restraint earns more trust than premature improvements
- **Avoid judging prematurely.** People will tell you who's "weak" in the first week; most of those calls turn out wrong once you've seen the context

## Actionable Insights

### Days 1–10: Learn the Business, Product, and Context

**Focus:** Domain knowledge before people opinions.

- Product roadmap and customer use cases
- Domain-specific risks being addressed (e.g., for an AI/security org: model abuse, data leakage, supply chain)
- Regulatory and compliance expectations
- Review architecture, data flows, and security boundaries
- Study current metrics: delivery predictability, reliability, security posture

### Days 11–20: Build Trust & Assess Team Health

**Focus:** People and execution reality.

- 1:1s with **all direct reports** and key senior engineers
- Skip-levels to understand morale, ownership, and pain points
- On-call load, incident patterns, and burnout risk
- Collaboration health with Product, Research, Security, and GTM (or your equivalents)

### Days 21–30: Align, Clarify, and Set Direction

**Focus:** Alignment and early signals.

- Validate roadmap assumptions with Product and Security leadership
- Identify 1–2 execution bottlenecks (don't try to find more — you can't fix more than 2)
- Align with leadership on:
  - Success metrics for the next quarter
  - Decision-making expectations and escalation paths
- **Day 30 readout to the team** — three sections:
  1. **What's working well**
  2. **What needs focus**
  3. **What I will not change yet** ← the most underrated section

> **Rule:** Avoid judging prematurely. First-week opinions of who's strong/weak are wrong ~30% of the time. Wait for the context you don't have yet.

### Days 31–60: Align, Prioritize, and Enable Execution

**Focus areas:**
- **Alignment & Strategy** — commit to a quarter-shaped plan with leadership
- **Team Health & Structure** — staffing changes, on-call rotations, scope clarity per person
- **Execution & Quality** — fix the 1–2 bottlenecks from Day 30
- **Deliverables by Day 60** — written plan, named owners, named dates

### Days 61–90: Execute, Scale, and Institutionalize

**Focus areas:**
- **Delivery & Outcomes** — ship 1–2 visible wins from the Day 60 plan
- **Technical & Security Maturity** — invest in the systems that will outlive your tenure
- **Leadership & Culture** — the patterns that emerge in Q1 set the norms for Q2–Q4
- **Deliverables by Day 90** — concrete shipped outcomes plus the institutional improvement (rubric, on-call rotation, review cadence) that will compound

## STAR Example: M&A Integration POC

A concrete template for how to talk about a managed delivery sprint in an interview:

- **S — Situation:** M&A had not yet closed. Data-security threats integration into Prisma Cloud needed planning.
- **T — Task:** Deliver a POC demo within two weeks of M&A closing (M&A was ~2 months out).
- **A — Action:** Watched the target product's demos and read documentation as if we were going to build on it. Drafted REST contracts, SLAs, pagination patterns, supported asset types and entity relations — and built our side of the integration code preemptively.
- **R — Result:** POC demo delivered within two weeks of close, covering ~70% of asset and alert types.

The lesson: **don't wait for inputs you can predict.** Build what you can ahead of the dependency.

## Anti-Patterns

- **The Reorg in Week 2** — almost never the right call; you don't yet know enough to redraw boundaries
- **Promising Anything by Day 30** — promises made in the learning phase get cashed in the execution phase, often at a loss
- **Skipping skip-levels** — the report-level view is filtered; the IC view is the unfiltered reality
- **Optimizing Day 30 to *look* like progress** — judge a 90-day arc on Day 90, not Day 30

## See Also

- [decoding-the-ask.md](decoding-the-ask.md) — read what people actually mean during 1:1s and skip-levels
- [managing-overwhelm.md](managing-overwhelm.md) — the EM-squeeze dynamics likely waiting for you on Day 1
- [meeting-recaps-and-decision-power.md](meeting-recaps-and-decision-power.md) — make every meeting count by leaving with a written outcome
- [team-health-checks.md](team-health-checks.md) — the diagnostic framework for "Days 11–20: Build Trust & Assess Team Health"
- [engineering-management-interview-prep.md](engineering-management-interview-prep.md) — interview-prep context

---

**Source:** /Users/vimittal/Downloads/leadership.html (imported personal notes)
**Date:** 2026-06-15
**Tags:** leadership, onboarding, 30-60-90-day-plan, new-manager, engineering-management, interview-prep, star-framework, skip-levels, judging-prematurely
