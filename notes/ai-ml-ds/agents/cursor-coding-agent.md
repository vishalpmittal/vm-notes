# Cursor's Coding Agent — Composer and the Production Architecture

How Cursor built **Composer** — a Mixture-of-Experts model specifically trained on edit trajectories — plus the surrounding infrastructure (router, tool harness, sandboxes) that makes agentic coding fast enough to feel interactive.

## Key Takeaways

- **Composer is a MoE model trained on edit trajectories**, not a general-purpose LLM. The training emphasizes (original_code, edit_command, final_code) triples and heavy search-and-replace tool use — turning "use tools" into a *learned behavior*, not a *prompt instruction*
- **The diff problem**: general models struggle with precise edits. Cursor fixed this by training on diff data with tens of thousands of GPUs, biasing the model toward search-and-replace tool calls
- **Compounded latency in agent loops** is solved with three levers: MoE for conditional compute, **speculative decoding** (small draft model proposes tokens; big model verifies), **context compaction** (summarize working state, drop irrelevant parts)
- **Hundreds of thousands of concurrent sandboxed coding environments** during training — required custom VM scheduling treating sandboxes as core infrastructure with fast provisioning and aggressive recycling
- The strategic insights: **tool use must be model-trained (not prompt-instructed)**, **adoption beats benchmarks**, **speed is a product feature** — route simple tasks to fast models, reserve the big model for planning

## Architecture Overview

```
   User input
       ↓
   Router (model selection — fast model for simple, Composer for complex)
       ↓
   Orchestrator (control loop)
       ↓ ↑
   Tool harness (10+ tools: read, search, edit, run terminal, run tests, etc.)
       ↑
   Context retrieval system (RAG + working memory)
       ↑
   Sandboxed execution environment
```

## Composer: A Model Trained for the Job

The fundamental difference vs Claude Code / Codex (which call general-purpose LLMs): **Cursor trained its own model for the specific shape of coding-agent work.**

### The Diff Problem

General models predict the *next token of the full edited file*. This wastes most of the prediction on unchanged code and frequently introduces subtle errors (changing whitespace, mis-indenting, dropping a parenthesis).

Cursor's training approach: emphasize **search-and-replace tool calls** over re-emitting whole files. Train on (original, edit_command, final) triples so the model learns to:
- Identify the precise edit
- Issue a tool call to perform it
- Not hallucinate surrounding context

This shifted the model from "rewriter" to "editor" — and reliability improved dramatically. Training compute: tens of thousands of GPUs.

### Mixture-of-Experts (MoE)

Composer is MoE — conditional compute where only a subset of experts activate per token. Benefits:

- **Per-token compute is much less than the full parameter count** — faster
- Different experts specialize on different code patterns naturally during training
- Easier to scale total capacity without proportionally scaling inference cost

## The Three Production Challenges

### 1. The Diff Problem (solved by training)
See above. Training-time fix, not a runtime fix.

### 2. Compounded Latency in Agent Loops

An agent makes 10 tool calls. Each one waits for an LLM response. If each response takes 3 seconds, the user waits 30 seconds.

Three latency-reduction techniques:

| Technique | What | Effect |
|---|---|---|
| **MoE architecture** | Conditional routing means per-token compute is small | Faster per-token throughput |
| **Speculative decoding** | Small draft model proposes N tokens; big model verifies in parallel | Effectively predicts N tokens per big-model pass |
| **Context compaction** | Summarize working state; drop irrelevant prior context | Smaller prompt = faster prefill |

Combined, these turn what would be a sluggish agent into one that feels interactive.

### 3. Sandboxing at Scale

Training required **hundreds of thousands of concurrent sandboxed coding environments** — each running real code, running tests, observing outcomes.

This isn't a typical workload. Cursor built:
- **Custom VM scheduling** for fast provisioning
- **Aggressive recycling** — tear down + recreate fast enough to serve constant throughput
- Sandboxes as **first-class infrastructure**, not afterthought ops

This parallels [Stripe Minions' devbox pooling](stripe-minions.md) — both teams discovered that the agent's value depends on cheap, fast, isolated execution environments.

## Strategic Insights

### 1. Tool Use Must Be Model-Trained
Prompt-engineered tool use (telling the model "when you want to edit, output this format") is brittle. **Training the model to use tools** — including which tool, when, with what arguments — produces vastly more reliable behavior.

This is part of why Anthropic, OpenAI, and now Cursor invest in tool-use-specific training data.

### 2. Adoption Metrics > Benchmarks
Cursor optimizes for what actually drives user retention (latency, edit accuracy, conversation rounds-to-success) rather than benchmark numbers (HumanEval pass rate). The two often diverge.

### 3. Speed Is a Product Feature
The router that picks "fast model for simple, big model for complex" is not just cost optimization — it's a UX decision. A response in 800ms feels like a coding assistant. A response in 8s feels like a chat with a slow colleague.

Cursor's stack reflects this: speculative decoding, MoE, model routing — all optimize for time-to-useful-output, not just quality.

## How Cursor Compares to Claude Code, Codex, Minions

| | Cursor (Composer) | Claude Code | OpenAI Codex | Stripe Minions |
|---|---|---|---|---|
| **Model** | Custom-trained MoE | Anthropic Claude | OpenAI GPT family | Forked goose |
| **Deployment** | IDE-embedded | Terminal CLI | App Server + multi-surface | Unattended Slack→PR |
| **Custom training?** | Yes (composer) | No | No (built on GPT) | No |
| **Sandbox infra** | Custom VM scheduling | User's local | User's local | Pre-warmed devboxes |
| **Tool training** | Heavy (edit trajectories) | Conventional | Conventional | Goose-defined tools |
| **Use case** | Interactive IDE editing | Multi-modal terminal work | IDE + web + macOS | Parallel unattended PRs |

The trend: **dedicated training for coding agents** is becoming table stakes. General-purpose LLMs work but specialized models for the task shape (edit trajectories, tool use) win on reliability and speed.

## What This Suggests for Building Your Own Coding Agent

Most teams won't train a Composer-class model. Still, the architectural lessons transfer:

1. **Train (or fine-tune) for your specific task shape.** Even small LoRA fine-tunes on your tool-call format help
2. **Optimize for latency, not just quality.** Speculative decoding, smaller models for simple tasks, async UI
3. **Sandbox infrastructure is real engineering work.** Don't treat it as "we'll wrap subprocess and hope"
4. **MoE is becoming the default architecture.** For agent workloads, conditional compute is essential
5. **Router > one big model.** Match model size to task complexity

## Related

- [OpenAI Codex](openai-codex.md) — adjacent coding-agent architecture
- [Stripe Minions](stripe-minions.md) — sandbox infrastructure done well; same lesson about devboxes
- [Claude Code architecture](../claude/claude-code-architecture.md) — third major coding-agent surface
- [Harness engineering](harness-engineering.md) — OpenAI's framing of building infrastructure around coding agents
- [Transformer architecture § MoE](../concepts/transformer-architecture.md#modern-attention-variants-the-efficiency-ladder) — MoE in the broader architecture space
- [Agents across SDLC](agents-across-sdlc.md) — where coding agents win/lose

---

**Source:** https://blog.bytebytego.com/p/how-cursor-shipped-its-coding-agent
**Date:** 2026-06-05
**Tags:** cursor, composer, coding-agents, mixture-of-experts, speculative-decoding, context-compaction, edit-trajectories, sandboxes, agent-architecture, case-study
