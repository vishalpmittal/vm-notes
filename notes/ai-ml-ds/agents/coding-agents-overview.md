# Coding Agents — Comparison and Overview

An index for the cluster of coding-agent notes in this repo. Same agent loop underneath; different deployment surfaces, training strategies, and operational models.

## Key Takeaways

- All modern coding agents share the same **agent loop** (LLM + tools + iterate). Differentiation is in the surrounding architecture, training, and deployment shape
- Three deployment patterns dominate: **interactive terminal/IDE** (Claude Code, Cursor, Codex), **unattended one-shot** (Stripe Minions), **organization-wide harness** (OpenAI internal)
- **Specialization is rising** — Cursor trains its own Composer model on edit trajectories. The frontier is shifting from "use a general LLM" to "train for the agent's task shape"
- **Infrastructure beats model quality** — Stripe Minions, OpenAI Harness, and Cursor all converge on the lesson that fast devboxes/sandboxes + observability + mechanical invariants matter more than picking the smartest model
- **Same agent loop, different surfaces:** context file (`CLAUDE.md` / `AGENTS.md` / rules), tool harness, MCP integration, plan/execute split

## Comparison Matrix

| | Claude Code | OpenAI Codex | Cursor (Composer) | Stripe Minions | OpenAI Harness |
|---|---|---|---|---|---|
| **Model** | Anthropic Claude | OpenAI GPT family | Custom-trained MoE (Composer) | Forked goose | OpenAI GPT |
| **Deployment** | Terminal CLI + web | App Server + JSON-RPC (VS Code, web, macOS, JetBrains, Xcode) | IDE-embedded | Unattended Slack→PR | Internal team practice |
| **Mode** | Interactive | Interactive | Interactive | Unattended one-shot | Mixed |
| **Context file** | `CLAUDE.md` | `AGENTS.md` | `.cursor/rules/` | Same as humans (Cursor/Claude format) | `AGENTS.md` as docs/ index |
| **Tools** | Built-in + MCP + Skills + Plugins | Built-in tools, MCP, custom | 10+ tools, trained-in usage | ~500 tools via Toolshed MCP | Codex tools + custom |
| **Training** | General-purpose Claude | General-purpose GPT | **Custom: edit trajectories on 10K+ GPUs** | None (uses goose+Claude/GPT) | None |
| **Sandbox** | User's local | User's local | Custom VM scheduling (hundreds of thousands of envs) | Pre-warmed 10s devboxes | Per-worktree bootable app + observability stack |
| **Scale stat** | 1M-token context (Opus 4.6) | ~6h autonomous runs | 100K+ concurrent training envs | **1,300+ PRs/week, zero human-written code** | 1M LOC / 1,500 PRs / 5 months |
| **Best for** | Interactive multi-file work | IDE-embedded coding across surfaces | Interactive IDE editing with speed | Parallel-stackable tickets, bug fixes | Greenfield org with disciplined harness |

## Deeper Reading

| Topic | Note |
|---|---|
| **Claude Code architecture** (internals) | [claude/claude-code-architecture.md](../claude/claude-code-architecture.md) |
| **Claude Code features** (12-feature reference) | [claude/claude-code-features.md](../claude/claude-code-features.md) |
| **Claude Code workflow tips** (daily-use + Boris's setup) | [claude/claude-code-workflow.md](../claude/claude-code-workflow.md) |
| **Claude Code source-leak critique** (skeptical take) | [claude/claude-code-source-leak-critique.md](../claude/claude-code-source-leak-critique.md) |
| **Claude internals** (how the model itself thinks) | [claude/claude-internals-interpretability.md](../claude/claude-internals-interpretability.md) |
| **OpenAI Codex architecture** | [openai-codex.md](openai-codex.md) |
| **OpenAI Harness Engineering** (how they build around Codex) | [harness-engineering.md](harness-engineering.md) |
| **Cursor Composer** | [cursor-coding-agent.md](cursor-coding-agent.md) |
| **Stripe Minions** | [stripe-minions.md](stripe-minions.md) |

## Cross-Cutting Patterns

What all of these converge on:

1. **Agent loop**: input → LLM → tool call OR final answer → execute → result → repeat
2. **Project-context file** (`CLAUDE.md` / `AGENTS.md`) as versioned per-repo instructions
3. **MCP for tool integration** — the emerging standard
4. **Plan/execute split** — Plan Mode in Claude Code, Composer's planning phase, Codex's plan-before-action
5. **Observable feedback loop** — running the app, tests, browser to verify (highest-leverage tip across all)
6. **Sandbox/devbox investment** — Stripe Minions pre-warming, Cursor's VM scheduling, OpenAI's per-worktree bootable apps

## Broader Context

- [Agents across SDLC](agents-across-sdlc.md) — where coding agents win/lose in the dev lifecycle (with adoption metrics, incident-rate data)
- [AI-native engineering](ai-native-engineering.md) — orchestrating AI agents as a paradigm shift (ADLC vs SDLC, 40/20/40 split)
- [Agentic design patterns](agentic-design-patterns.md) — the escalation ladder (workflow → agent → multi-agent)
- [AI agent anatomy](ai-agent-anatomy.md) — the canonical 6-component model
- [LLM tool use and MCP](../concepts/llm-tool-use-and-mcp.md) — the integration protocol all of these use
- [AI trends 2026 § coding](../concepts/ai-trends-2026.md#trend-3-coding) — the trajectory

---

**Date:** 2026-06-05
**Tags:** coding-agents, claude-code, openai-codex, cursor, composer, stripe-minions, openai-harness, index, comparison
