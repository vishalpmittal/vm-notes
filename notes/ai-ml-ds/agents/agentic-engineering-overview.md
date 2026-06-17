# Agentic Engineering — 30 Concepts Across 6 Layers

A field-overview map of agentic engineering as of mid-2026. Six layers (foundations → configuration → capability → orchestration → guardrails → observability) covering 30 core concepts. The thesis: learn the ideas, let tools come and go. New models and frameworks ship constantly; the same few ideas recur.

## Key Takeaways

- An agent is **"an LLM in a loop with tools that decides what to do next"** — use them when next steps depend on prior outcomes, skip them when a single prompt or plain script would do
- The field stacks into 6 layers: **Foundations** (what an agent is), **Configuration** (rules + skills), **Capability** (tools + MCP), **Orchestration** (subagents + loops), **Guardrails** (sandbox + permissions + hooks), **Observability** (traces + logs + replay)
- **Context rot is real and measurable** — Claude Opus 4.7 drops from 59.2% at 256K tokens to 32.2% at 1M tokens; every token in config files and memory competes for attention
- **Config + skills beats bigger model** — Haiku with curated skills scored 27.7% on SkillsBench vs Opus without skills at 22.0%. The leverage is in instructions, not parameters
- **Recommended starting order:** (1) write project config file, (2) wire MCP for live docs, (3) turn default sandbox on, (4) use subagents for read-heavy work

## The Six Layers

| Layer | Purpose | Concepts |
|---|---|---|
| **Foundations** | What an agent is and how it runs | Agent definition, ReAct loop, state, patterns |
| **Configuration** | Shape behavior before code is written | Config files, skills, frameworks, caching, context rot |
| **Capability** | Define reachable resources | MCP, live docs, AI-native search, visual outputs, memory, knowledge search |
| **Orchestration** | Coordinate multiple agents | Subagents, agent loops, orchestration tools, managed agents |
| **Guardrails** | Prevent damage when judgment fails | Sandboxing, permissions, hooks, prompt injection defense, structural linting, pre-commit, CI/CD |
| **Observability** | Reveal what happened | Tracing, logging, replay, metrics |

## Layer 1: Foundations

### Agent definition

A large language model that runs in a loop, has access to tools, and decides what to do next. Generates chains of actions where each step builds on previous results.

- **Use when:** tasks are open-ended, next steps depend on prior outcomes, solution path can't be scripted
- **Skip when:** single prompt suffices, plain script works, looping costs outweigh benefits

### Execution model (ReAct loop)

Three repeating steps: **Think** (read state, decide) → **Act** (emit tool call, harness executes) → **Observe** (result joins context). Failure recovery is built in — a failing test returns a stack trace; next iteration plans against the actual result rather than a prediction.

Variations: parallelism (multiple tool calls per act step), blocking vs non-blocking execution.

### Agent state

Splits into two categories with very different properties:

| Category | Properties | Storage |
|---|---|---|
| **Context window** | Hard ceiling (token limit), soft ceiling (context rot), disappears at session end | In-memory |
| **External state** | Fetched on demand, persists across sessions | Files (diffable), memory (facts), databases (multi-process) |

Multiple agents → use **git worktrees** so each gets an isolated working copy while sharing `.git`.

### Common patterns

- **Planner/Executor** — one agent plans, another executes; keeps context aligned to each role's strengths
- **Router/Specialist** — router dispatches to bounded specialists (security reviewer, debug expert); cheaper smaller models
- **Map-Reduce Parallelism** — fan out subagents (e.g., one per file for PR review), merge results; cost bounded by longest subagent

Patterns compound: planner → router → map-reduce → merge → review.

See: [Agentic Design Patterns](agentic-design-patterns.md), [AI Agent Anatomy](ai-agent-anatomy.md).

## Layer 2: Configuration

### Config files (`CLAUDE.md`, `AGENTS.md`)

Project-level Markdown loaded at session start, kept in context window. Encodes standards, limits, conventions, behavioral rules.

**Best practices:** keep under 100 lines, treat as code (not docs), cut anything not consistently improving outcomes. Without a config, agents default to "whatever looked plausible in training data" → mismatched dependencies, formatters, defensive sludge.

### Reusable workflow files (skills)

On-demand Markdown with YAML frontmatter controlling when they load. Frontmatter components: `name` and `description` signal relevance; optional `globs` narrows by file type/path.

**Division of labor:** Config = always-relevant rules. Skills = task-specific procedures. Live prompt = current task uniqueness.

Striking result: **Haiku + skills (27.7%) beat Opus without skills (22.0%)** on SkillsBench. Cheaper model with good instructions outperforms flagship.

### Workflow frameworks

Document processes for planning, TDD, debugging, review. Prevent fallback to training-data defaults.

- **Superpowers** — curated skill library with HARD-GATE directives and anti-rationalization tables
- **Get Shit Done (GSD)** — slash commands, hooks, meta-prompting
- **Compound Engineering** — Plan → Work → Review → Compound phases, captures patterns into searchable knowledge base

### Prompt caching

Stores stable conversation prefixes so models don't re-read them each turn. First call pays a small premium to write cache; subsequent turns re-read at steep discount. Justifies longer config files since re-read costs approach "rounding error."

**TTL tradeoff:** longer windows survive bigger activity gaps but cost more to write; shorter windows expire during pauses (coffee, Slack, spec review).

### Context rot

Model performance degrades as context window fills. Measured drops:

| Model | Short docs | At 256K | At 1M |
|---|---|---|---|
| GPT-5.5 | 98.1% | — | 74.0% |
| Claude Opus 4.7 | — | 59.2% | 32.2% |

**Root cause:** transformers split finite attention across more material; each token competes with everything else. Compounded by training distribution — models learned most from shorter contexts, so very long contexts involve extrapolation.

**Takeaway:** keep config files, skills, memory lean. Every token competes for attention.

See: [Context Engineering](../concepts/context-engineering.md).

## Layer 3: Capability

### Model Context Protocol (MCP)

Standard giving agents access to external tools without hand-rolled glue per tool/agent. By 2026, deferred tool loading is standard — agent starts with tool names/descriptions; full schema loads only when used.

Token cost comparison (three-tool stack):

| Approach | Tokens |
|---|---|
| Deferred MCP | ~607 |
| Fully loaded MCP | ~5,500 |
| Plain CLI skills | ~300 |

MCP solves real problems (standardization, auth, permissions, distribution) despite being heavier than leanest alternative.

See: [LLM Tool Use and MCP](../concepts/llm-tool-use-and-mcp.md), [MCP vs Agent Skills](../concepts/mcp-vs-agent-skills.md).

### Live document retrieval

Models have knowledge cutoffs → confidently call renamed, moved, deprecated functions without help.

- **Context7** — pulls current library docs into context with working examples
- **DeepWiki** — AI-powered docs for GitHub repos via natural language queries

Pattern: separate "knowing how to code" from "knowing current state."

### AI-native web search

Traditional search returns human-friendly HTML → agents chew through ads/nav. AI-native search returns structured results directly.

**Example — Exa:** structured results, extracted content, highlights, summaries. Enables finding docs/discussions/real-world examples beyond training data. Token efficiency matters in automated workflows.

### Visual output generation

Skills/MCPs extend agents into specialized formats:

- **Figma MCP** — reads design data (layout, components, variables); generates code from specs or pushes back to canvas
- **frontend-slides skill** — self-contained HTML presentations with inline CSS/JS
- **draw.io** — architecture diagrams from Terraform; regenerate via CI
- **Remotion** — video code

Pattern: skill/MCP knows target format; agent writes code for it.

### Persistent memory

Sessions restart fresh — decisions and context vanish daily.

- **Flat memory:** `MEMORY.md` in project directory; loaded at session start, agent writes back. Sits in system prompt → subject to context pressure
- **Episodic Memory plugin:** indexes past conversation files, embeds as vectors, exposes semantic search via MCP

**Progression:** start with flat memory file → reach for searchable layer when grep becomes insufficient.

See: [Agent Memory State Consistency](agent-memory-state-consistency.md).

### Knowledge search

Useful context exists outside agent sessions (meeting notes, design docs, specs).

**Example — QMD:** on-device search engine for broader knowledge base, exposed via MCP.

**Distinction:** persistent memory = agent-learned over time. Knowledge search = materials agent never helped produce.

## Layer 4: Orchestration

### Subagents

Delegated workers with bounded scope: separate prompt, limited toolset, clean context window. Only compressed result returns to parent — not full reasoning chain.

**Benefits:**
- **Parallelism** — multiple subagents run simultaneously
- **Compression** — test output, long docs, side quests stay in subagent context; main thread stays clean

**Collision handling:** git worktrees solve parallel write conflicts. Claude Code supports `isolation: "worktree"` natively.

Definition example:

```yaml
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: sonnet
```

### Agent loops

Re-run a single agent with fresh context each iteration, tracking progress in files/git. Context window never fills with stale reasoning. Effective for: migrating codebases file-by-file, processing queues, refactoring across call sites.

**Claude Code implementation:** `/goal` command sets completion conditions ("all tests pass, lint clean"). Evaluator checks after each turn, stops when met.

See: [Loop Engineering](loop-engineering.md).

### Orchestration tools

Managing many parallel agents requires coordination → prevent duplication, progress loss, incompatible results.

- **Conductor** — single UI for parallel sessions in isolated workspaces, built-in diff viewer
- **JetBrains Air** — same concept within JetBrains using Docker/git worktrees
- **Vibe Kanban** — standalone app with kanban UI for agent tasks
- **Cline Kanban** — works across agents (Claude Code, Codex, Cline) with auto-commit and dependency-aware parallelization
- **Paperclip** — "orchestration layer for zero-human companies" with org charts, delegation, budget ceilings, governance board

### Managed / cloud-hosted agents

Long-running sessions on vendor infrastructure via API. Vendor provides harness, sandbox, tool loop, container. App sends events via SSE, receives streamed tool calls/messages.

**Billing:** pay-as-you-go API tokens vs Claude Pro/Max plans. Plans more efficient for personal use; API better for products running agents for others.

See: [Harness Engineering](harness-engineering.md) (OpenAI's playbook).

## Layer 5: Guardrails

### Sandboxing

Isolates what agents can read, write, or access over network. Modern harnesses use bubblewrap (Linux) or Apple Sandbox (macOS).

**Standard restrictions:**
- Read: project directory, system libraries
- Write: project directory, sandboxed temp
- Network: allowed list only
- Block: `/.ssh`, `/.aws`, `~/.gnupg`, `~/.docker`

**Enhanced:** Docker with `--network none` = full isolation (no host filesystem beyond mounted project, no credentials, no outbound).

**At scale:** Cloudflare Dynamic Workers — per-execution sandboxes starting in milliseconds, blocked network, credentials injected server-side.

**Prevents:** blast radius from other guardrails failing.

### Permissions

Allow/deny lists for tool calls, file reads, shell commands. Baseline for what agents can do without prompting.

Agents seek reward-hacking shortcuts: `chmod 777` on permission errors, `curl | sh` for installs, commented-out assertions for failing tests, force-push to main on blocked pushes.

**Two-layer approach:**
- **Project-level** (committed): common ops (lint, test, git) without prompting
- **User-level deny-list:** never permitted (`.env` reads, `rm -rf`, force-push to main, `curl * | sh`)

**Automation:** permission classifier (small model) evaluates each tool call, lets through standard calls, holds others for review. Claude Code's Auto mode exemplifies this.

### Hooks

Handlers at specific workflow points. **Pre-tool hook** fires after agent assembles tool call but before execution — last inspection point.

**Shell security example — Tirith validator catches:** hostname homoglyphs, path homoglyphs, insecure transport, ANSI injection, pipe-to-shell patterns, environment manipulation.

Generic mechanism — extensible to any tool (file edits, MCP calls).

### Prompt injection defense

Agents treat config files and tool outputs as ground truth → obvious attack vectors.

**Supply-chain version:** poisoned config file in cloned repo with instructions to pipe outputs to attacker endpoint. **Never auto-load MCP servers from cloned repositories** (MCP servers = arbitrary code with agent permissions).

**Character homoglyph attacks:** Latin "i" (ASCII 105) vs Cyrillic "і" (Unicode 1110) — identical visually, shell treats differently.

**General defense:** assume agents receive untrustworthy input. Inspect inputs (config files, MCP servers) and outputs (commands) before damage.

See: [Prompt Injection Defenses](../concepts/prompt-injection-defenses.md), [AI Security Platform](../concepts/ai-security-platform.md).

### Structural code linting

Traditional linters work on surface (formatting, imports, naming). Structural linting works on syntax trees → meaning, not characters.

**AST-grep example:** catches `def process(items=[])` where default list is created once and shared across calls — pattern LLMs learned without understanding danger.

**Process:** identify anti-patterns → encode as rules with tests → wire into pre-commit + CI. Agent hits gate, reads error, tries again.

### Pre-commit gates

Local quality gates blocking bad code before commits.

**Four-layer config:**
1. Standard hooks (trailing whitespace, valid YAML/TOML, file size)
2. Ruff for linting/formatting (with `--fix` auto-correction)
3. Bandit for security (hardcoded passwords, `eval()`)
4. AST-grep for structural rules

**Agent advantage:** can't ignore rules, promise to fix later, or get annoyed. Hits gate, reads error, rewrites, retries.

### CI/CD gates

Second automation layer. Pre-commit runs locally on laptop; CI runs on shared server when code pushes. Fresh server clones repo, runs same checks. Enforcement where agent cannot skip.

**Practical note:** add `concurrency` block canceling stale runs when new push arrives — agents produce prolific branch commits.

## Layer 6: Observability

### Tracing

Step-by-step run record structured for navigation.

**What useful traces show:**
- Tree of tool calls (subagent A → tool X → tool Y)
- Duration per step
- Inputs and outputs at every node
- Model's stated reasoning at each decision

**Structure matters:** flat tool call lists are hard to follow; trees show reasoning. Sources: harness logs basics; LangSmith, Helicone, OpenTelemetry tracer for deeper.

### Logging

Raw, append-only record of inputs/outputs/tool calls per run. Foundation that traces, replay, metrics build on.

**Minimum useful set:**
- Every model call: full prompt, response, latency, tokens, model version
- Every tool call: name, parameters, result, latency
- Every error
- Session ID tying them together

**Format:** verbose, append-only, JSON Lines. Keep everything, trim later — losing inputs to unexplained outcomes is worse than storage bills.

### Replay / debugging failed runs

Complete session logs enable reruns:

- **Reproduce bugs:** replay with recorded model responses → exact same trace for harness/tool failures
- **Test fixes:** replay with fresh model calls → see whether harness/prompt/tool changes fix failures
- **Model-specific bugs:** test across model versions

### Metrics

Most measurable agent data is **proxy data** falling out of logs: latency per session, latency per tool call, token + dollar cost, tool call counts, failure counts. Catches obvious failure modes (sessions running up bills, agents stuck in loops).

**Outcome data is harder** — success isn't something agent can self-report. Anchor to external artifacts: passing CI tests, merged PRs, non-rolled-back deploys.

## Recommended Starting Order

1. **Write project config file** (Layer 2) — biggest behavior shift for lowest effort
2. **Wire MCP for live docs** (Layer 3) — eliminates "calls deprecated APIs" failure mode
3. **Turn default sandbox on** (Layer 5) — bounds the blast radius before doing more
4. **Start using subagents for read-heavy work** (Layer 4) — protects context window

## The Underlying Principle

> Learn the ideas, let tools come and go. New models, frameworks, launches appear constantly — chasing all wastes time. The same few ideas recur. Master them, and tool selection matters less.

**Configuration shapes behavior before code is written. Capability defines reachable resources. Orchestration coordinates multiple agents. Guardrails prevent damage. Observability reveals what happened.**

The pace will not slow.

---

**Source:** https://newsletter.systemdesign.one/p/agentic-engineering
**Date:** 2026-06-17
**Tags:** agents, agentic-engineering, harness, mcp, subagents, sandboxing, prompt-caching, context-rot, observability
