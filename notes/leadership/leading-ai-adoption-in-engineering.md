# Leading AI Adoption in Engineering

## Key Takeaways

- AI won't reduce engineering headcount — software demand keeps rising while development costs fall; the role evolves but total demand for builders likely increases
- Adoption mandates are unnecessary — adoption accelerates naturally; the real work is removing friction and providing learning time
- Most adoption challenges are fundamentally human: incentives, anxiety, learning time, and changing metrics — not tooling
- High-performing engineers maintain quality standards regardless of tools (amplification effect) — AI doesn't inherently create more technical debt, but "cognitive debt" (understanding systems less) is an emerging concern
- The biggest bottlenecks aren't code review or velocity — they're decision-making, prioritization, and meetings
- The AI-native SDLC inverts time allocation: majority of effort shifts to planning and validation, while creation and operations compress
- The cost of building collapsed but the cost of aligning organizationally has not — when multiple teams can solve the same problem in parallel, coordination becomes the bottleneck
- **AI didn't add friction — it revealed it.** Velocity, cognitive, and knowledge friction were always there but masked by slow build speeds. AI removed the speed limit; now friction *is* the bottleneck. **AI generates code, not context** — and most context still lives in individual heads

## Actionable Insights

**Don't mandate, enable:**

- Remove friction (tool selection, configuration, access)
- Allocate explicit learning time — group learning sprints outperform individual learning
- Leaders must create space for capability building, not force usage
- Celebrate concrete success stories publicly; let organic champion networks emerge

**Reframe the headcount question:**

- Smaller companies gain faster market entry with AI tools — the competitive landscape shifts, not shrinks
- Engineering will increasingly involve orchestrating AI agents rather than direct coding — different skillsets needed: delegation, context-switching, intent definition
- Not all engineers should become "agent managers" — professional identity matters

**Watch for cognitive debt:**

- Code volume increases with AI, but the debt-to-output ratio is debatable
- The real risk: engineers understanding their own systems less deeply
- High performers amplify quality with AI; struggling engineers amplify problems
- Non-engineer code contributions (designers submitting PRs) require robust test suites and deployment checks as prerequisites

**On AI-generated code expectations:**

- 50% of *new* code within five years is plausible
- Rewriting existing legacy systems lacks economic justification — distinguish new code from total codebase
- Risk concerns reflect rational engineering accountability, not resistance

## Designing the AI-Native Engineering Org

**SDLC shift:** Traditional split was ~80% operations, 10-15% creation. Top-performing teams now spend majority of time on planning and validation, while AI compresses creation and operations. Don't fully automate validation and security — human oversight remains critical.

**Org structure — modify execution, not org charts:**

- Atlassian: shifted to 3-4 person squads for new projects
- Microsoft: eight-week mission-specific teams focused on rapid learning cycles
- 1Password: compressed planning horizons from 12-18 months to single quarters

**Manage AI costs like cloud costs:**

- Map token expenditure by repository and project
- Negotiate volume commitments with providers
- Build internal cost-tracking tools
- Allocate budget for experimental learning despite uncertain ROI

**Engineer skills evolution (3-5 year horizon):**

- Maker's mindset — tool-agnostic, oriented toward business outcomes
- Generalist capabilities spanning product and engineering
- Agency — decisiveness without management oversight
- Higher-level abstraction — making better choices about *what* to build, not just building faster

## Panel Insights (DX Annual Debate)

A panel of practitioner-researchers (Etsy, Twilio, GitHub, Google, DX) debated AI productivity. Beyond the points above, the discussion surfaced four specific findings worth pulling out:

- **Manager-engineer disconnect** — most engineering managers think AI usage is a valid performance metric; engineers strongly reject this framing. Measure outcomes (merge time, defects), not tool usage.
- **Perceived vs actual review speed** — at Twilio, high-AI-usage devs reported worse review turnaround but achieved *faster actual merge times*. Perception of review delay worsens as coding time shrinks (it becomes proportionally more of the bottleneck even when it didn't change).
- **The diagnostic question** — ask engineers directly: *"Are you learning anything?"* Surfaces unsustainable pace and missed learning time better than productivity metrics.
- **Risk metaphor** — *"If we had a butter knife and now you have a chainsaw, the risks are different."* (Rafe Colburn, Etsy) — capability shift demands a different safety posture, not just more output.

## DX Q1 2026 Impact Report

DX's quarterly report on AI in engineering (400+ companies, +40% dataset vs Q4 2025) adds hard numbers and a **reversal** worth flagging.

### Headline Numbers

| Metric | Value | Note |
|---|---|---|
| Industry AI adoption | 93% | Saturation, not early-adopter |
| Manager code output (daily AI users) | **4x vs 6 months ago** | Up from 2x in Q4 2025 — doubling |
| Junior engineers time saved/week | **4.9 hours** | Now leading |
| Staff+ engineers time saved/week | 4.8 hours | Now trailing — *reversal* |
| Change-failure rate volatility | ±2pp swings | = ~50% defect-rate jump |

### Junior-Senior Reversal (Important Update)

The Sustainability Problem section below originally cited research that **AI enhanced senior roles more than junior**. Q1 2026 data reverses this: **juniors now save more hours per week (4.9) than Staff+ engineers (4.8)**.

Possible explanation: as agentic workflows mature, the leverage shifts to less-experienced engineers who have more room to be augmented. Seniors hit a ceiling earlier because their work is already high-context.

Implication: the "junior pipeline will shrink" prediction may be wrong. Juniors with AI are the most-leveraged tier; the question is whether org structures recognize this.

### Player-Coach Manager Re-Emerging

Engineering managers using AI daily ship **4x more code than 6 months ago** (up from 2x in Q4 2025). The trend is doubling. This re-opens the "player-coach" debate: managers who code daily without dropping people work.

**Don't restructure the org chart on a single quarter's data.** But the trajectory is clear enough to pilot the model intentionally rather than letting it happen by accident.

### Quality Volatility — Tests Are Now a Precondition

A ~2 percentage point swing in change-failure rate is a **50% jump in defect rate**. With AI-amplified throughput, defects scale with output. Automated testing is no longer a "best practice" — it's the precondition for safely using AI tools at all.

### Shadow AI

Developers circumventing enterprise tooling is now a named risk category. The mitigation: **reduce friction on the sanctioned path so the easy path is the safe path**. Banning shadow AI by policy doesn't work; engineers route around enforcement.

### Smaller Companies Win on Efficiency

<200-developer orgs outpace large enterprises in AI efficiency gains. Likely causes: shorter feedback loops, less procurement friction, fewer mandated tools. Enterprises hoping to "out-tool" the gap will lose; they need to out-iterate.

### The "No Average" Warning

> "There is no 'average' experience with AI impact." — DX Q1 2026

Per-org outcomes are asymmetric. Industry averages should **supplement, not replace** internal telemetry. Build Q1-vs-Q4-vs-your-baseline comparisons rather than chasing headline numbers.

### Notable Side Finding

**Rust** showed notable time-savings improvement attributed to reasoning-model / agentic-workflow advances. Languages that are hard to type and easy to verify benefit disproportionately from AI assistance.

## The Sustainability Problem for Senior Engineers

AI tools enhanced senior roles more than junior ones — seniors have the system-level understanding to apply AI across the full lifecycle, then execute themselves. But productivity gains didn't reduce workload; organizational expectations expanded to absorb increased output.

**What expanded:**

- Senior engineers now code most days (previously occasional prototypes) while also increasing strategic writing and meetings
- Idea-to-prototype compressed from a year to weeks, but stakeholder alignment didn't speed up proportionally
- Thinking time — supposedly central to senior roles — nearly vanished, only appearing during holidays

**What's at risk:**

- 1-on-1 mentoring and people-focused work doesn't benefit from AI tooling and gets squeezed out
- Specializing in AI/GenAI creates career advantages but narrows expertise — "identified with one thing in a way I wasn't before"
- The role expanded in contradictory directions: deeper hands-on engineering + broader strategic scope + more mentoring — unsustainable simultaneously

---

## When Enthusiasts and Skeptics Stop Talking (Charity Majors)

A specific dysfunction worth naming: most engineering orgs now have **two distinct camps** that have stopped communicating productively about AI.

### The Two Races

| Camp | What they're racing against |
|---|---|
| **AI Enthusiasts** | **Time** — competitors using AI effectively could put them out of business |
| **AI Skeptics** | **Entropy** — shipping unreviewed code creates technical debt and degrades systems |

Both face genuine existential threats. Both are right. But they've started caricaturing each other ("you hate progress" / "you don't care about quality") and operate in separate realities.

### The Structural Asymmetry

The feedback loop is broken because **winners and costs flow to different people**:

- An enthusiast ships fast → celebrated for velocity
- Downstream teams discover the chaos weeks later → frustrated
- Skeptics see the problems but lack credibility with leadership
- Enthusiasts never see the second-order costs of their wins

This is why mutual dismissal calcifies — both sides experience only their own evidence.

### The Three Fixes

**1. Tell the whole story in the same forum.** Present wins *alongside* costs where both camps can see them. Enthusiasts invite feedback openly; skeptics close loops by surfacing problems back to creators, not just complaining in side channels.

**2. Treat the disagreement as engineering, not rhetoric.** Replace accusations with diagnostic questions:
- ❌ "You hate progress" / "You don't care about quality"
- ✅ **"What conditions would make you comfortable shipping unreviewed code?"**

Then map dependencies, identify prerequisites, sequence the work. The disagreement becomes a roadmap.

**3. Build credible expertise on both sides.** Engineers who influence outcomes need standing through *knowledge*, not position:
- **Skeptics must understand AI opportunities** to have moral authority to push back
- **Enthusiasts must understand the operational realities** they're creating for downstream teams
- Leaders earn trust by forcing decisions **only as a last resort** — when both sides genuinely understand each other, they usually converge

### The North-Star Example

Fin (formerly Intercom) achieved **3× output in 9 months** through disciplined AI adoption — not magic. The unlock wasn't the AI itself; it was the prerequisites:
- Better tests
- Feature flags
- Observability
- Decoupled dependencies

This validates both camps. Enthusiasts get the speedup; skeptics get the conditions for it to be safe. The "right" answer is enthusiasm grounded in skeptics' prerequisites.

### Lesson for Leaders

When you see your org polarizing into camps, don't pick a side. **Force the diagnostic conversation**: what would each side need to agree the other side has a point? The answer is usually a list of investments (testing, observability, gated rollouts, paved paths) that are valuable independent of who "wins" the argument.

---

## AI Reveals (Doesn't Add) Friction (Forsgren / Clegg, LDX3 2026)

Nicole Forsgren (lead author of *Accelerate*, now Sr. Eng Director at Google) reframed the AI-productivity debate at LDX3 2026: friction was always the bottleneck — slow builds, ambiguous "done", siloed knowledge — but natural speed limits hid it. AI removes the speed limit, and now everything else is the bottleneck.

![AI didn't add friction. It revealed it. Friction along a slow road becomes the bottleneck when AI accelerates. "AI generates the code. Not the context."](../images/20260609-1402-ldx3-ai-revealed-friction.png)

### Three Kinds of Friction

![Three kinds of friction — Velocity (slow builds, manual gates, 15-min deploys → fix: DORA + automation), Cognitive (unclear done, context-switching → fix: explicit ready/done criteria), Knowledge (context lives in one person's head → fix: ADRs + ownership maps). Knowledge is "the one most teams underinvest in"](../images/20260609-1403-ldx3-three-kinds-of-friction.png)

| Friction | What it looks like | Fix |
|---|---|---|
| **Velocity** | Slow builds, manual approval gates, 15-min+ deploys | DORA metrics + automation |
| **Cognitive** | Unclear "done", constant context-switching, ambiguous criteria | Explicit ready/done criteria |
| **Knowledge** | Context lives in one person's head; engineers unfamiliar with adjacent systems | **ADRs + ownership maps** — the one most teams underinvest in |

### The Central Insight

> "AI generates the code. Not the context." — Nicole Forsgren

In most organizations, critical context lives in individual heads — practically inaccessible to AI. Knowledge friction therefore becomes the **highest-leverage investment** as AI adoption scales.

### Wise's Approach (Rick Clegg)

Wise built scoped AI tools embedded in workflows — a code-flow visualizer that generates architectural diagrams, a PR-review assistant that explains changes in plain language.

> "The goal wasn't speed — it was making systems legible to anyone needing to work within them. Speed follows from clarity."

**Lesson:** target AI tooling at *legibility* (the knowledge-friction lever) before targeting speed. Speed is the downstream consequence.

### Actionable

- **Audit your team's friction by type** — which of the three is biggest? Most teams will discover knowledge friction is dominant once AI exposes the others
- **Invest in ADRs and ownership maps** *before* investing in more AI tooling — context-less AI just produces more output your team can't validate
- **Frame AI tools as legibility multipliers**, not speed multipliers — diagram generators, change explainers, onboarding agents
- **Watch for the trap of measuring AI ROI by code volume.** If knowledge friction is unchanged, volume gains become technical debt at scale

---

**Source:** https://newsletter.getdx.com/p/ai-productivity-debate
**Source:** https://newsletter.getdx.com/p/designing-the-ai-native-engineering
**Source:** https://newsletter.getdx.com/p/ai-assisted-engineering-q1-2026-impact
**Source:** https://jamiehurst.co.uk/2026-05-24_ai-sustainable
**Source:** https://charitydotwtf.substack.com/p/ai-enthusiasts-are-in-a-race-against
**Source:** https://www.blog4ems.com/p/engineering-leadership-lessons-from-ldx3-2026
**Date:** 2026-05-29 (initial), 2026-06-07 (Charity Majors enthusiast/skeptic), 2026-06-09 (Forsgren/Clegg friction reveal)
**Tags:** leadership, ai-adoption, developer-productivity, engineering-management, org-design, ai-native, sustainability, senior-engineering, agentic-workflows, performance-metrics, player-coach, shadow-ai, change-failure-rate, dx-data, enthusiast-skeptic, organizational-polarization, charity-majors, friction-types, knowledge-friction, forsgren, ldx3
