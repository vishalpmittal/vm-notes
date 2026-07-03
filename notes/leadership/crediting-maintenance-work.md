# Crediting Maintenance Work in Performance Reviews

## Key Takeaways

- Most engineering rubrics measure what was delivered (features, launches), not the background work that keeps systems running -- creating a structural bias against maintenance-focused engineers.
- During calibration, the engineer who shipped a customer-facing service gets easy visibility, while the one who prevented an outage via a database migration, mentored juniors, and wrote documentation struggles to compete -- despite equal organizational impact.
- Maintenance work (migrations, stability improvements, mentoring, documentation) is invisible by default; managers must make it visible deliberately or it will be systematically undervalued.
- Rubrics that don't explicitly account for maintenance contributions push high-performers toward feature work and away from the unglamorous work that prevents incidents.
- Individual productivity metrics trigger a predictable collaboration collapse: engineers redirect from untracked collaborative work toward measurable individual tasks — **the output numbers rise while the team's actual capacity silently degrades**

## Actionable Insights

- **Audit your rubric for maintenance blind spots.** Review each level's criteria and ask: "Could someone who only did maintenance work this cycle meet these expectations?" If not, revise the language to include system health, reliability, and knowledge-sharing contributions.
- **Document maintenance impact in business terms.** Frame migrations as "prevented X hours of downtime" and documentation as "reduced onboarding time by Y days." Translate invisible work into outcomes calibration panels can compare against feature launches.
- **Pre-seed calibration narratives.** Before reviews, write up maintenance contributions with the same specificity you'd use for feature launches -- scope, complexity, impact, and risk. Don't rely on the work speaking for itself.
- **Create explicit recognition for "keeping the lights on."** Whether through a rubric dimension, a dedicated award, or simply calling it out in team channels -- signal that maintenance work is valued equally to new development.

## Why Individual Metrics Destroy Collaboration (Goodhart + Campbell)

Individual productivity metrics are structurally counterproductive: when output is tracked per person, engineers redirect effort from collaborative activities toward measurable individual tasks. **The output numbers go up because the helping went down.**

Collaborative behaviors collapse in a specific sequence, ranked by team benefit vs. personal credit earned:

1. **Unblocking colleagues** (first casualty) — pure altruism with zero personal attribution
2. **Root cause analysis** — investigation generates metrics for bugs filed, not for understanding gained
3. **Code review depth** — substantive review consumes time with no visible individual output
4. **Mentoring and pairing** — teaching hours subtract directly from individual productivity numbers

What follows is metric gaming: cherry-picked easy tickets, artificially split PRs, inflated task estimates.

### Why Better Individual Metrics Don't Fix This

- **Goodhart's Law:** "When a measure becomes a target, it stops being a good measure." The metric stops tracking the underlying behavior once engineers know they're being evaluated on it.
- **Campbell's Law (1976):** The more any quantitative indicator is used for decision-making, the more it corrupts both the metric and degrades the underlying work.

> "If you choose to create incentives around measures, you will never again receive accurate data." — Kent Beck

The path forward is not to find better individual metrics — it's to measure at the team level, where unblocking, reviewing, and mentoring carry the same incentive weight as shipping individual code.

---

**Source:** https://www.blog4ems.com/p/crediting-maintenance-work-in-performance-reviews
**Source:** https://www.blog4ems.com/p/on-measuring-engineers-as-individuals
**Date:** 2026-05-05, updated 2026-06-30
**Tags:** performance-reviews, maintenance-work, engineering-management, calibration, rubrics, individual-metrics, goodharts-law, campbells-law, collaboration, team-metrics
