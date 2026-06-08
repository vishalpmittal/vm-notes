# Engineering Velocity and Feedback Loops

Two complementary essays — Patrick Dubroy on "fast is better than slow" and Thorsten Ball on "building software is learning" — converge on the same underlying claim: **the bottleneck isn't writing code, it's how fast you can learn whether what you wrote was right.**

## Key Takeaways

- **The best programmers aren't fast because they're great — they're great because they're fast.** Speed isn't hustle; it's a compounding advantage that enables more learning cycles per unit time (Dubroy)
- **Building software is fundamentally learning.** Misalignment between request and delivery is *normal* on novel work — the question is how fast you can detect and correct it (Thorsten Ball)
- The diagnostic question reframes everything: **"How can I get feedback on what I'm building as soon as possible?"** Every other optimization is downstream of this
- **70% rule** — ship/publish when reasonably satisfied, not when perfect. The next 30% comes from feedback you can't get without shipping
- **Skip imagined future needs.** Deliver exactly what was asked, learn what's actually needed, then iterate

## Actionable Insights

### Eliminate Delay
- Start tackling problems **immediately** rather than postponing — waiting almost never makes things easier
- Use **small time chunks** instead of waiting for perfect uninterrupted blocks. Reclaim the fragmented hours
- **Ship the first commit** before you're done thinking about the whole thing

### Optimize for Feedback Speed
- **Build prototypes in hours, not weeks.** A bad prototype today beats a good one in two weeks
- **Vary feedback mechanisms** — specs, demos, example code, README-driven APIs, daily deploys. Each gives different signal
- **Right-size scope** — focus on uncertain areas; skip parts you already understand
- **Ship incrementally** — one commit at a time so it's clear what changed when feedback arrives

### Reduce Friction Around You
- **Share early work** (70% rule) — invite feedback when there's still time to use it
- **Ask for help** — leverage team expertise rather than solving everything alone
- **Skip minor battles** — accept reviewer feedback on inconsequential issues rather than arguing

### Defer Speculation
- **Do the minimum requested.** Build for what's asked, not imagined future needs
- The cost of over-engineering compounds: more code to maintain, more decisions calcified, more options foreclosed
- **Optimize for learning, not for being right the first time** — being right the first time on novel work is mostly luck

## The Underlying Mental Model

Software engineering looks like:

```
think → code → test → ship → learn → think → code → ...
```

The naive optimization is to make each step faster. The better optimization is to **shorten the entire loop and run it more times**. Five 10-minute loops generate vastly more learning than one 50-minute loop.

This is why:
- **Fast feedback** > deep deliberation on novel work
- **Small commits** > big PRs
- **Real users > internal review** for product-shape questions
- **Compiler errors / failing tests** > running it all in your head
- **Daily deploys** > monthly releases

## Why "Fast" Doesn't Mean "Sloppy"

The misconception: fast → sloppy → bad code → tech debt.

The actual mechanism: fast → more learning cycles → more course corrections → better final state. Speed *enables* quality, doesn't trade against it, on work where the right answer isn't known upfront.

Speed is bad when:
- The problem is well-understood and rushing skips known constraints
- Critical paths (safety, security, money) where mistakes have asymmetric cost
- You're racing to commit prematurely instead of testing assumptions

Speed is good when:
- Novel work where the spec will evolve based on what you build
- Iterative product work where users will tell you what's right
- Skill-building / learning — speed = more reps = faster expertise growth

The skill is recognizing which mode you're in.

## The "Building Software Is Learning" Frame

Thorsten Ball's reframe is important: **the misalignment between request and delivery isn't a failure of communication — it's intrinsic to novel work**.

You can't perfectly spec something you've never built. The spec evolves *as you build it*, because the act of building reveals constraints, edge cases, and possibilities that weren't visible before.

This means:
- **Premature specs** are nearly always wrong
- **Reviewers seeing the work** often shifts what they thought they wanted
- **The "true" requirements emerge** through prototype + feedback
- **The deliverable that gets shipped** rarely matches the original ask exactly

So the question isn't "did we deliver the spec?" but "did we converge on the right thing fast?" Velocity in this frame is the rate at which you traverse the requirements space.

## What This Connects To

These ideas show up across the repo under different framings:

- [Breaking procrastination](../life-coaching/breaking-procrastination.md) — same "lower activation energy → more iteration" pattern at the individual level
- [Outcome-driven engineering management](outcome-driven-engineering-management.md) — outcomes are detected through feedback; velocity is how fast you get there
- [Code review capacity budgeting](code-review-capacity-budgeting.md) — feedback bandwidth is finite; how you spend it matters
- [Managing overwhelm](managing-overwhelm.md) — speed via prioritization and saying no, not via working harder
- [Modern engineering values](modern-engineering-values.md) — "strict guardrails + fast feedback" is value #3
- [Memorable is useable](memorable-strategy.md) — same idea applied to strategy, not code
- [Distributed system failure modes](../system-design/distributed-system-failure-modes.md) — feedback loops on systems (not just code) — the same pattern at the architectural layer
- [Learning system design](../system-design/learning-system-design.md) — "study real postmortems" = optimize the feedback loop for systems intuition

---

**Source:** https://dubroy.com/blog/fast-is-better-than-slow
**Source:** https://registerspill.thorstenball.com/p/building-software-is-learning
**Date:** 2026-06-07
**Tags:** leadership, engineering-velocity, feedback-loops, learning, iteration, 70-percent-rule, productivity, engineering-practice
