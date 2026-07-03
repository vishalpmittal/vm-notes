# Project Categories and Prioritization

## Key Takeaways
- Engineering time for net-new work should target a 70/30 split: 70% on production (GA) projects and 30% on experimental (limited-availability) projects.
- All projects start as experiments with a defined hypothesis and time-boxed evaluation window; they either graduate to production or are sunset with documented learnings.
- A 12-point practical prioritization filter provides a sequential decision model — from non-negotiable commitments (items 1–3) down to timing/market window (item 12).
- A healthy team workload targets a 60:20:20 ratio — 60% shippable product work, 20% keep-the-lights-on (KTLO), 20% ideation and exploratory work.
- Three supplementary frameworks (RICE, Value vs. Effort matrix, MoSCoW) serve as tiebreakers when the primary filter needs more granularity.

## Project Categories

Engineering net-new time targets a **70/30 split** between production and experimental work.

### Production (~70% of net-new engineering time)

The highest deliverers of customer value — generally available (GA) projects continuously invested in over time. The team maintains only a few at any given time; most project work falls into an existing production project.

Characteristics:
- Dedicated infrastructure (for services)
- Off-hours alerting
- Long-term roadmap ownership

### Experimental (~30% of net-new engineering time)

Early-access projects that validate new ideas or fill capability gaps.

Characteristics:
- Small scope with a defined hypothesis
- Shared infrastructure (for services)
- Sunset or promote after a validation window

## Graduation Process

All projects start as experiments and graduate to production only after sufficient adoption and validation.

```
Experiment → Evaluate → [Validated] → Production
                      → [Not validated] → Sunset
```

### Experiment Phase Requirements
- Define a hypothesis and success metrics upfront
- Keep scope minimal — validate the concept, not the full feature set
- Set a time-bound evaluation window (e.g., one quarter)

### Evaluation Criteria
- Has the hypothesis been validated by customer usage or feedback?
- Are product teams actively depending on or requesting this capability?
- Does it align with one of the team's core value propositions?

### Graduation to Production
1. Present adoption data and customer signal to the team
2. Meet the GA bar and operations bar for the project
3. Commit to ongoing investment, roadmap ownership, and operational support
4. Transition from experimental to production-grade infrastructure

### Sunset Path
If the hypothesis is disproven or adoption is insufficient:
1. Document learnings
2. Communicate the decision to stakeholders
3. Clean up infrastructure
4. Redirect engineering time

## Project Prioritization

Prioritizing features is a balancing act — juggling what users want, what the business needs, and what the team can realistically deliver.

Before prioritizing, answer the **5 W's** (What, Why, When, Where, Who) for each project to identify appropriate weights.

### The 12-Point Practical Filter

Run items through 1–3 first to eliminate or elevate, then use 4–7 to rank what's left. Items 8–12 are tiebreakers. For each project, assign the priority tier of the first category that applies.

1. **Customer/contractual commitments** — already promised, non-negotiable
2. **Regulatory/compliance deadlines** — legal exposure if missed
3. **Strategic alignment** — does this move the organization's stated goals?
4. **Breadth of impact** — how many users/customers are affected
5. **Revenue impact** — new ARR, retention risk, upsell potential
6. **Dependency sequencing** — unblocks other high-value work
7. **Confidence in the problem** — evidence quality (data > interviews > assumptions)
8. **Effort relative to value** — ROI; low-effort high-value beats high-effort medium-value
9. **Tech debt / reliability** — often underweighted until it causes an incident
10. **Competitive necessity** — table-stakes parity vs. differentiation
11. **Reversibility** — low-confidence bets should be cheap to undo
12. **Timing / market window** — decays fast; only relevant if truly time-sensitive

## Healthy Team Ratio

An ideal workload split across all engineering work:

| Bucket | Target |
|---|---|
| Shippable product work | 60% |
| Running the business (KTLO) | 20% |
| Ideation and exploratory work | 20% |

This is distinct from the 70/30 net-new split — it applies across all engineering work including maintenance.

## Prioritization Frameworks

Use these as tiebreakers when the 12-point filter needs more granularity:

| Framework | Best For | Required Input | Major Drawback |
|---|---|---|---|
| **RICE** | Data-driven roadmap optimization | Analytics, reach metrics, reliable estimates | Time-consuming; requires good data |
| **Value vs. Effort** | Quick alignment and finding quick wins | Team intuition, qualitative insights | Highly subjective |
| **MoSCoW** | Strict deadlines and MVP scoping | Clear business rules, fixed timelines | Doesn't rank within categories |

### RICE Framework

**RICE Score = (Reach × Impact × Confidence) / Effort**

- **Reach** — users impacted in a given timeframe
- **Impact** — massive / high / medium / low
- **Confidence** — expressed as a percentage
- **Effort** — person-months

### Value vs. Effort Matrix

| | Low Effort | High Effort |
|---|---|---|
| **High Value** | Quick Wins — do these first | Major Projects — plan carefully |
| **Low Value** | Fill-ins — do when bandwidth allows | Thankless Tasks — drop entirely |

### MoSCoW Method

- **Must have** — non-negotiable; release fails without it
- **Should have** — important but not vital for this release
- **Could have** — nice-to-have if time permits
- **Won't have** — explicitly deprioritized for this cycle

---

**Source:** Imported from internal team documentation
**Date:** 2026-06-30
**Tags:** project-management, prioritization, product-management, engineering-strategy, roadmap, frameworks, rice, moscow, agile
