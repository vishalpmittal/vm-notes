# Agents Across the SDLC

## Key Takeaways

- Agents win where machines provide **fast, objective feedback** (code, test, maintain) and lose where only humans can judge tradeoffs (design, deploy)
- AI adoption empirically hurts stability: Google DORA 2024/2025 shows lower stability with more AI use; Faros AI saw a **242% rise in production incidents per merged change** across 22,000 developers
- Quality declines well before context windows fill — every model in an 18-model study degraded with larger inputs; 1M-token models slipped at 50k
- Developers accept AI review suggestions at **16.6% vs. 56.5%** for human reviews (278,790 comments studied) — AI review is first-pass screening, not replacement
- The real frontier isn't generation quality, it's **shared memory across the SDLC** — multiple agents duplicate work and overwrite each other without it (Carlini's 16-agent C-compiler experiment)

## Agent vs Chatbot vs Autocomplete

| Tier | Pattern | Autonomy |
|---|---|---|
| **Autocomplete** | Line/block completion | None — human types, AI suggests |
| **Chatbot** | Q&A, answer-and-copy | Low — human asks, AI answers, human acts |
| **Agent** | Autonomous act-learn-decide loop with tools and context | High — AI takes actions, observes results, iterates |

## The Eight Stages of Software Development

| Stage | Agent fit | Reason |
|---|---|---|
| **1. Plan** | Assist only | Business context is undocumented; humans decide what to build |
| **2. Design** | Avoid | Duplication (8x post-AI) and over-engineering |
| **3. Code** | ✅ Strong | Fast feedback (compile, lint, test), verifiable |
| **4. Test** | ✅ Strong (logic) / ⚠️ Weak (UI) | Tests self-grade; UI needs 4-step verification loop |
| **5. Review** | ✅ First-pass only | 16.6% acceptance — useful filter, not replacement |
| **6. Deploy** | ❌ Human-owned | Blast radius too large; rollback is real-time, risky |
| **7. Operate** | ✅ Investigate / ❌ Diagnose | Agents pull facts; humans diagnose under confidence-without-accuracy risk |
| **8. Maintain** | ✅ Strong | Self-grading (tests pass = done) — Dependabot-style work |

### Stage 1: Plan

Agents can read codebases, map change impact, draft tickets. **Humans still decide what to build** because business context lives in heads and slack threads, not docs.

> "Someone has to prompt the Claudes, talk to customers, coordinate with other teams, and decide what to build next." — Boris Cherny

### Stage 2: Design (avoid)

Two failure modes:

- **Duplication**: GitClear shows 5+-line duplicate blocks grew **8x post-AI in 2024**
- **Over-engineering**: AI bloats abstractions and leaves dead code

> "Models like to overcomplicate, bloat their abstractions, and leave dead code." — Andrej Karpathy

### Stage 3: Code (strong)

Where agents shine — fast objective feedback (compile, lint, run tests). Best practices:

- **Rules files** (e.g., `CLAUDE.md`) document conventions, test/lint commands, pitfalls
- **Spec-driven development**: spec → agent plans → execute one task at a time → human reviews each step
- **Manage context-window quality decline** — split work before quality degrades (often well below the stated context limit)

Market signal: Cursor went $100M → $1B ARR in a year.

### Stage 4: Test

**Logic tests:** strong. Momentic runs 200M test steps/month and finds 390k+ bugs. Diffblue Cover generates Java tests 250x faster with 50–70% coverage lift.

**UI tests:** weak. Requires a 4-step verification loop (screenshot → click → verify → assert) vs the one-step logic check.

Common failure: agents generate empty/passing tests, or test explosion (hundreds of trivial tests, no coverage of real edge cases).

### Stage 5: Review (first-pass only)

- CodeRabbit deployed across 6M+ repos / 15k+ teams
- AI suggestion acceptance: **16.6%** (vs 56.5% for humans)
- Useful as first-pass filter; false-positive volume drowns signal if treated as authoritative

### Stage 6: Deploy (humans only)

Bad deploys break everything simultaneously; rollback is real-time and risky. Agents can stage/check, not execute the final push.

### Stage 7: Operate

- Agents **investigate** — Datadog Bits AI SRE pulls facts before engineers wake
- Humans **diagnose** — agents have confidence-without-accuracy risk on novel incidents
- Stability degradation cited above is concentrated here when agents are over-trusted

### Stage 8: Maintain (strong)

Maintenance work self-grades. Dependabot at scale; GitHub data shows faster critical-CVE remediation with automated dependency updates. Watch for cumulative quality drift over months.

## The Core Two-Axis Rule

```
                Verifiable          Not Verifiable
              ┌─────────────────┬──────────────────┐
   Fast       │                 │                  │
   feedback   │  AGENT ✓        │  CAREFUL ⚠       │
              │  (code, test,   │  (operate —      │
              │  maintain)      │  agents inves-   │
              │                 │  tigate only)    │
              ├─────────────────┼──────────────────┤
   Slow       │  CAREFUL ⚠      │  HUMAN ✗         │
   feedback   │  (review —      │  (design,        │
              │  first-pass     │  deploy, plan    │
              │  only)          │  decisions)      │
              └─────────────────┴──────────────────┘
```

**Use agents where machines grade output objectively and fast. Reserve humans where judgment dominates.**

## Shared Memory: The Real Frontier

Without shared memory, agents collide and duplicate work.

**Carlini's 16-agent C-compiler experiment:**

> "Every agent would hit the same bug, fix that bug, and then overwrite each other's changes. Having 16 agents running didn't help because each was stuck solving the same task." — Nicholas Carlini

**Solution direction:** shared memory across the SDLC, anchored in channels teams already use (Slack threads, PR comments, ticket history). The core problem is **context loss across tools/tickets/reviews/Slack**, not code generation quality.

## Three Safety Measures Before Granting Autonomy

| Measure | What it does |
|---|---|
| **Sandbox** | Agent runs in isolated environment; can't touch prod |
| **Scoped permissions** | Agent has only the access it needs for the task |
| **Audit log** | Every agent action is logged and reviewable |

## The Speed Paradox

METR 2025: developers using AI heavily are **19% slower** in measured throughput but **feel 20% faster**. Implication: don't trust the feeling; measure outcomes.

## Where to Start

1. Coding (with rules files + spec-driven dev)
2. Maintenance (Dependabot-style work)
3. Test (logic tests first; defer UI tests)
4. Review (first-pass only; humans still own decisions)

**Avoid until trust is earned:** design, deploy.

## See Also

- [ai-agent-teams.md](ai-agent-teams.md) — 7-role taxonomy for multi-agent systems
- [agentic-design-patterns.md](agentic-design-patterns.md) — escalation ladder (direct API → workflows → agents → multi-agent)
- [../concepts/llm-tool-use-and-mcp.md](../concepts/llm-tool-use-and-mcp.md) — how agents use tools
- [../concepts/ai-assisted-code-review.md](../concepts/ai-assisted-code-review.md) — deeper on AI for code review

---

**Source:** https://newsletter.systemdesign.one/p/agentic-ai-use-cases
**Date:** 2026-06-01
**Tags:** ai-agents, agentic-ai, sdlc, claude-code, cursor, ai-code-review, ai-testing, shared-memory, context-engineering, dev-productivity
