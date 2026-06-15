# Team Health Checks

How to read a team's actual condition — not just velocity metrics — and act on it before issues become attrition or outages. The **Spotify Squad Health Check** is the canonical framework; layer on signal categories (Delivery / Technical / Morale / Alignment) as you mature.

## Key Takeaways

- **Spotify's Squad Health Check** is a traffic-light grid (red / yellow / green) across 9 dimensions × N teams. The value is **comparative + longitudinal** — track quarter-over-quarter, not single snapshots
- The **9 dimensions** balance execution (Mission, Easy to Release, Code Health, Speed) with people (Psychological Safety, Learning, Fun, Pawns/Players, Support/Tools)
- Beyond the framework, a healthy read needs **four signal categories**: Delivery & Execution, Technical & System, Morale & Team Dynamics, Alignment & Purpose
- The most overlooked dimensions are **Psychological Safety** (can engineers safely surface risks?) and **Ability to say no / negotiate scope** — both are leading indicators of attrition
- The **casual 1:1 walk** is the highest-signal data collection — formal surveys miss what people will tell you in motion

## Actionable Insights

### Use the Squad Health Traffic-Light Grid

| Indicator | Team A | Team B | Team C | Team D |
|---|---|---|---|---|
| Mission | 🟢 | 🟢 | 🟢 | 🟢 |
| Easy to Release | 🟢 | 🔴 | 🔴 | 🟡 |
| Code Health | 🟡 | 🟢 | 🟡 | 🟢 |
| Speed | | | | |
| Learning | | | | |
| Product Autonomy | | | | |
| Psychological Safety | 🔴 | 🟢 | 🟢 | 🟢 |
| Support/Tools | | | | |
| Fun | | | | |
| Pawns/Players | | | | |

**How to use:** run quarterly; engineers self-rate; compare team-to-team and quarter-to-quarter. The trend matters more than any single cell.

### Watch the Four Signal Categories

#### Delivery & Execution Health

- **Predictability** — delivery success %
- **Flow metrics** — deployment frequency, cycle time, throughput, incident response time
- **Quality trends** — defect escape rate, bugs, regressions
- **Tradeoff posture** — balance among quality, tech debt, and features

#### Technical & System Health

- Architecture clarity and clear ownership per system
- Reliability metrics — SLOs, incident counts, MTTR

#### Morale & Team Dynamics

- **Psychological safety** — are engineers comfortable disagreeing and surfacing risks?
- Engagement and morale — measured via 1:1s, skip-levels, retros
- **Casual 1:1 walks** — the format people open up in
- Role clarity and load balance
- Growth signals — mentorship, promotions, learning velocity

#### Alignment & Purpose

- Clear goals tied to customer outcomes
- Roadmap clarity at team level
- Stakeholder trust (do partners come *to* the team or *around* the team?)
- Ability to say no, negotiate scope

### Common Failure Modes

- **Rating once, never trending** — a single quarter of greens means nothing without the prior quarter
- **Confusing velocity with health** — a team shipping fast can be quietly burning out
- **Skipping the Pawns/Players column** — "are people executing someone else's plan or shaping it?" is the autonomy signal that predicts attrition
- **Manager-rating instead of engineer-rating** — the team's own self-rating is the data; your interpretation is the action

## See Also

- [people-development-and-coaching.md](people-development-and-coaching.md) — what to do once the health-check flags a person-level issue
- [managing-overwhelm.md](managing-overwhelm.md) — adjacent: the EM squeeze counterpart for leaders themselves
- [outcome-driven-engineering-management.md](outcome-driven-engineering-management.md) — activity-vs-outcome framing for the Delivery & Execution category
- [code-review-capacity-budgeting.md](code-review-capacity-budgeting.md) — reviewer attention as a hidden health signal
- [engineering-velocity-and-feedback.md](engineering-velocity-and-feedback.md) — the feedback-loop lens on execution health
- [engineering-management-interview-prep.md](engineering-management-interview-prep.md) — interview-prep context for this material

---

**Source:** /Users/vimittal/Downloads/leadership.html (imported personal notes)
**Date:** 2026-06-15
**Tags:** leadership, team-health, spotify-squad-health, psychological-safety, engineering-management, signals, trending, 1-on-1, morale, delivery-metrics
