# Engineering Leadership Maxims

Compact one-liners that compress a lot of hard-earned senior-engineering judgment. Use them as **diagnostic prompts** in meetings, reviews, and 1:1s — "is this what's happening here?"

## Key Takeaways

- Most engineering-leadership problems show up as **slow decisions, ambiguous asks, and bad team interfaces** — not bad code
- **"On Tuesday, I will" is the dividing line** between aspiration and execution. If a sentence doesn't have a date and an owner, it isn't a plan
- **Reliability and observability are product features**, not afterthoughts — design them in, staff them, track them with the same rigor as anything user-facing
- **Adding a team adds edges, not just nodes** — coordination cost grows faster than headcount; every new team interface needs deliberate design
- **AI makes drafts cheap; taste becomes expensive** — the binding constraint shifts from "can we produce this?" to "can we tell whether this is good?"

## The Maxims (Grouped by Theme)

### Decision Quality

- **"If you can't say what decision you're asking for, you're not ready for the meeting."** → Every meeting agenda item should be either *informational* or *here is the decision I need*. Drift means you're conducting group therapy
- **"'We should' is not a plan. 'On Tuesday, I will' is a plan."** → Plan = owner + date + first action. Strip the modal verbs; anything else is wishful aspiration
- **"Slow code is sometimes a symptom. Slow decisions are always a problem."** → Code latency is debuggable. Decision latency compounds invisibly and rarely shows up on dashboards
- **"The best escalation comes with a proposal."** → "Help, this is broken" creates a problem for your manager. "Here's the situation, I propose X, I need your decision by Friday" is the senior version of the same message

### Engineering Quality

- **"The best engineers pick the right problems to solve."** → Selection beats execution at every senior level. Sharp execution on the wrong problem produces wasted excellence
- **"Reliability is a product feature. Treat it like one."** → Resource it, prioritize it on the roadmap, measure it with SLOs, ship reliability improvements like ship features. The opposite is "we'll get to it after this launch" — which means never
- **"Make observability part of the feature."** → If you ship a feature without dashboards, alerts, and traces, you've shipped a black box. The shipping-with-observability rule should be as load-bearing as shipping-with-tests
- **"Small PRs are kindness. Especially if the PR is AI generated."** → Reviewer capacity is the new bottleneck. A 200-line PR gets a real review; a 2,000-line PR gets a rubber stamp. AI-generated code makes the asymmetry sharper, not gentler (see [code-review-capacity-budgeting.md](code-review-capacity-budgeting.md))
- **"The migration is never just a migration."** → It's also: rewriting the on-call runbook, retraining the team, finding three things nobody documented, rebuilding two integrations downstream, and discovering the data model was wrong all along. Budget accordingly

### Org & Team Dynamics

- **"You can't 'communication' your way out of a bad interface between teams."** → If team A and team B keep miscommunicating about the same boundary, the boundary itself is wrong. More syncs, better Slack channels, and clearer docs won't fix a misshapen interface. Re-cut the responsibilities
- **"Avoid hero culture. Build systems that don't require heroes."** → A system that depends on one person staying late is a fragile system, regardless of how much you praise the hero in the all-hands. Heroes are a symptom of missing automation, missing redundancy, or missing succession
- **"When you add a team, you add edges, not just nodes."** → The coordination cost of a new team isn't just N people doing work — it's N² interfaces with the existing teams. Every new edge needs deliberate ownership, contracts, and review cadences (see Conway's Law)
- **"Trust is a latency optimization for teams."** → High-trust teams can make decisions without re-litigating the assumptions each time. Low-trust teams pay coordination tax on every decision. Trust isn't soft — it's the throughput multiplier

### AI

- **"AI makes drafts cheap. Taste becomes expensive."** → When production is fast and free, the binding constraint shifts to **judgment about what's worth producing**. The senior engineer's leverage moves from "can write this fast" to "can tell whether this is right." Hiring, promotion, and review systems still optimize for the old constraint (see [code-review-capacity-budgeting.md](code-review-capacity-budgeting.md), [leading-ai-adoption-in-engineering.md](leading-ai-adoption-in-engineering.md))

## How to Use This List

- **In meetings:** when a decision feels stuck, ask *"What decision are we trying to make today?"* — if no one can name it, end the meeting and reconvene with that named
- **In reviews:** when someone says "we should improve X," follow up with *"By when, and who?"* — if those don't land, X won't happen
- **In planning:** when proposing a migration, force the team to list **three non-migration items** the migration will also require — staffing, training, documentation, dependency rebuilds. The pure-migration estimate is always wrong
- **In team growth:** when adding a team, write down the **new interfaces** before you write the hiring plan. The edges are the work
- **In hiring/promotion:** when "AI makes drafts cheap, taste becomes expensive" is true, evaluate candidates on *judgment* (what would you cut?) at least as heavily as production (what can you build?)

## See Also

- [software-engineering-laws.md](software-engineering-laws.md) — the **named** laws (Conway's, Brooks's, Goodhart's, etc.) that this complements
- [meeting-recaps-and-decision-power.md](meeting-recaps-and-decision-power.md) — the recap is what converts "we should" into "Tuesday I will"
- [code-review-capacity-budgeting.md](code-review-capacity-budgeting.md) — the operational consequence of "small PRs are kindness, especially if AI-generated"
- [leading-ai-adoption-in-engineering.md](leading-ai-adoption-in-engineering.md) — the broader context for "AI makes drafts cheap, taste becomes expensive"
- [outcome-driven-engineering-management.md](outcome-driven-engineering-management.md) — the institutional version of "the best engineers pick the right problems"
- [managing-up.md](managing-up.md) — the broader pattern behind "the best escalation comes with a proposal"
- [ownership-and-not-dropping-things.md](ownership-and-not-dropping-things.md) — the broader pattern behind "build systems that don't require heroes"

---

**Source:** User-shared engineering-leadership lessons (2026-06-14)
**Date:** 2026-06-14
**Tags:** leadership, engineering-leadership, maxims, decision-quality, reliability, observability, team-design, ai-engineering, escalation, taste, hero-culture, migrations, trust
