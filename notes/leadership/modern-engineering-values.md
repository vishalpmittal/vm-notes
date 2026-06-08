# Modern Engineering Values (in the AI-Agent Era)

Christoph Nakazawa's six values for engineering teams when **execution becomes cheap and judgment becomes scarce** — when agents can generate any code in any quantity, what differentiates good engineering?

## Key Takeaways

- **Execution is cheap; judgment is scarce.** Agents can generate unlimited code; what's valuable is knowing what's worth building and what to throw away
- The six values: **Strong Ownership, Taste, Strict Guardrails + Fast Feedback, Context in the Repo, Own Your Stack, Option Value**
- Each value is reframed for the agent era: ownership amplifies agent effectiveness, taste filters AI-generated noise, guardrails enable agent speed, context-in-repo benefits agents and humans equally
- Small teams (**2-3 people**) with clear ownership boundaries and isolated repositories move fastest — small teams with strong taste beat large teams with weak filters
- The bottleneck has shifted from coding capacity to **exercising discernment** — "I have a low tolerance for bullshit, and with agents, everyone can generate a lot of bullshit all day long"

## Actionable Insights

### 1. Strong Ownership
**Deep domain expertise amplifies agent effectiveness.** When you understand the architecture, product requirements, and long-term direction, you make confident decisions and steer agents productively. Without ownership, you're at the mercy of whatever the agent generates.

- Keep team size small (2-3 people)
- Draw clear ownership boundaries — who decides what
- Isolated repositories where possible (no shared blast radius)
- Long-term tenure in a domain compounds — rotation policies undermine this

### 2. Taste, Taste, Taste
> "I have a low tolerance for bullshit, and with agents, everyone can generate a lot of bullshit all day long."

Discriminating judgment about what's worth building separates signal from noise. Taste is the filter — without it, you ship more, but more of it is garbage.

- Train taste through reps and feedback (see [engineering-velocity-and-feedback.md](engineering-velocity-and-feedback.md))
- Be willing to reject AI-generated work that's plausible but wrong
- Make taste visible — code review comments, design critique, candid "this isn't good enough"

### 3. Strict Guardrails & Fast Feedback Loops
> "[Guardrails] are the difference between an agent completing their work in 1 minute or 60."

Enforce lint rules, automated testing, verification constraints. Tight feedback accelerates iteration — both for humans and for the agents doing iterative work.

- Pre-commit hooks, lint-on-save, fast CI
- Type checkers as first-class collaborators
- Property tests and contract tests where appropriate
- See [engineering-velocity-and-feedback.md](engineering-velocity-and-feedback.md) for the broader pattern

### 4. Context in the Repo
Consolidate scattered context (docs, implicit knowledge, production behavior) **locally within the codebase**. This benefits both agents and humans while injecting team values directly into code.

- `CLAUDE.md` / `AGENTS.md` files describing conventions
- Architecture docs co-located with the code
- Decision records (ADRs) capturing the "why"
- Inline comments where the **why** isn't obvious

The cost: docs that go stale. The mitigation: treat doc-rot as a real problem worth recurring agent runs to fix (see [harness-engineering.md](../ai-ml-ds/agents/harness-engineering.md) for OpenAI's "doc-gardening agent" pattern).

### 5. Own Your Stack
**Build or maintain core dependencies rather than outsourcing to third-party libraries.** Agents reduce ownership costs while restoring full control over the product and user experience.

- Pre-agent era: "don't reinvent the wheel" — third-party deps were almost always cheaper
- Agent era: in-house implementations cost less to maintain (agents do the maintenance), give full control, fit your needs precisely
- See [Cursor's choice to write its own concurrency-limiting helper](../ai-ml-ds/agents/cursor-coding-agent.md) instead of `p-limit` for the canonical example

### 6. Option Value
**Design systems that unlock more options for improvements and significant future changes**, enabling rapid adaptation as requirements shift.

- Loose coupling preserves options; tight coupling forecloses them
- Generic interfaces > overly specific abstractions
- Reversible decisions > irreversible ones (when in doubt, pick the reversible path)
- Don't optimize prematurely — keep the option to optimize later

## The Underlying Principle

In a world where code generation is free, what's scarce?

| Pre-agent era | Agent era |
|---|---|
| Writing code is bottleneck | **Knowing what to write** is bottleneck |
| Cost of dependencies low (third-party libs) | **Cost of dependencies higher** (lose control + agent can build it) |
| Senior eng = writes more code | Senior eng = **exercises better judgment** |
| Bigger team = more output | **Smaller team with stronger taste** = more output |
| Comprehensive specs reduce risk | **Fast iteration with feedback** reduces risk |

The values listed above all converge on this shift: **discernment, taste, and feedback loops become the differentiators** because everything else is commoditized.

## Related

- [AI-native engineering](../ai-ml-ds/agents/ai-native-engineering.md) — broader pattern of orchestrating AI agents (40% context / 20% generation / 40% review)
- [Leading AI adoption in engineering](leading-ai-adoption-in-engineering.md) — how to roll this out across an organization
- [Engineering velocity and feedback](engineering-velocity-and-feedback.md) — value #3 in depth
- [Memorable is useable](memorable-strategy.md) — applied to strategy/values themselves: the 6 values work only if your team can remember them
- [Harness engineering](../ai-ml-ds/agents/harness-engineering.md) — OpenAI's operational playbook that embodies most of these values
- [Stripe Minions](../ai-ml-ds/agents/stripe-minions.md) — Strong ownership + guardrails + context-in-repo as a production case
- [Claude Code workflow tips](../ai-ml-ds/claude/claude-code-workflow.md) — `CLAUDE.md` as the "context in the repo" mechanism
- [Cursor coding agent](../ai-ml-ds/agents/cursor-coding-agent.md) — "own your stack" example (Composer's training on edit trajectories, custom helpers)
- [Agents across SDLC](../ai-ml-ds/agents/agents-across-sdlc.md) — where these values pay off across the dev lifecycle

---

**Source:** https://cpojer.net/posts/modern-engineering-values
**Date:** 2026-06-07
**Tags:** leadership, engineering-values, ai-agents, taste, ownership, guardrails, feedback-loops, context, own-your-stack, option-value, christoph-pojer, nakazawa
