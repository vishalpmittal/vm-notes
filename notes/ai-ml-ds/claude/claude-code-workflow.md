# Claude Code: Workflow Tips

How to actually use Claude Code in daily work — from beginner setup through how Claude Code's own creator runs his fleet of agents.

## Key Takeaways

- **Treat Claude Code as a fleet, not a tool.** Boris (the creator) runs 5 terminal Claudes + 5-10 web Claudes in parallel — context-switching, not coding speed, is the bottleneck
- The core workflow loop is **Explore → Plan → Execute → Verify** — skip it and you'll waste a session debugging Claude's confidently-wrong code
- **CLAUDE.md is institutional knowledge that compounds.** Commit it to Git, update it multiple times a week, and have Claude itself review PRs and propose updates to it
- **Default to Opus with thinking.** Slower per call, but you steer less, so it's faster end-to-end on multi-step work
- The single highest-leverage tip: give Claude a **deterministic way to verify its own work** (browser test loop, agent-stop hooks). Claimed to 2-3x output quality
- **Compounded context becomes standing overhead.** Run `/context` periodically; prune bloated CLAUDE.md sections and unused skill packs — a lean, clear standing context outperforms a comprehensive one
- **Four tools can cut token usage by ~90%:** codegraph (code indexing), RTK (output compression), caveman (response trimming), and built-in session commands — each has tradeoffs that determine when to disable it

## Setup

Install via the one-liner for your OS:

```bash
# macOS / Linux
curl -fsSL https://claude.ai/install.sh | bash

# Homebrew
brew install --cask claude-code

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# WinGet
winget install Anthropic.ClaudeCode
```

Requirements: macOS 13+, Win10 1809+ (or WSL), Ubuntu 20.04+, 4GB RAM.

Run `claude` in any project folder; auth via browser. First action: `/init` to generate a starter `CLAUDE.md`.

## The Core Loop: Explore → Plan → Execute → Verify

![Plan mode workflow](../../images/20260605-1725-boris-plan-mode.png)

```
1. EXPLORE  (read-only)   — "map this directory, explain the data flow"
2. PLAN     (Shift+Tab)   — Plan Mode, no edits, produce a plan.md, review/edit
3. EXECUTE  (on a branch) — phased implementation, Esc to interrupt anytime
4. VERIFY                  — run tests, have Claude fix failures, review diffs
```

The four steps map to the four big risks:
- Skipping **Explore** → wrong assumptions about the codebase
- Skipping **Plan** → multi-file edits you didn't authorize
- Skipping **branch isolation in Execute** → can't unstick if it goes sideways
- Skipping **Verify** → "looks right" code that breaks at runtime

## CLAUDE.md — The Onboarding File

Generate with `/init`. Keep under ~150 lines. Sections that work in practice:

```markdown
# Project
One-paragraph description.

## Code Style
- Language, framework, conventions
- Lint/format rules

## Commands
- `pnpm dev` — start dev server
- `pnpm test` — run tests
- `pnpm lint` — lint

## Architecture
- Folder layout
- Major modules
- Data flow

## Rules
- NEVER commit `.env`
- Always use pnpm (not npm)
- Run tests before commits
```

Anthropic reports engineers onboard **80% faster** to new codebases with a solid CLAUDE.md.

### Update CLAUDE.md continuously

Every time Claude makes a mistake you have to correct, the correction belongs in CLAUDE.md. Treat the file as an evolving institutional record, not a one-time setup artifact.

**Compounding Engineering pattern** (Dan Shipper / Boris's team): tag `@claude` on PRs via the Claude Code GitHub Action. On review, ask Claude to propose CLAUDE.md updates based on what went wrong. Every PR becomes an opportunity to make the agent smarter for next time.

## Boris's Fleet: Running Claude in Parallel

![5 Claude instances in numbered terminal tabs](../../images/20260605-1720-boris-parallel-terminals.png)

The creator of Claude Code runs:
- **5 local Claudes** in numbered terminal tabs (tabs 1-5), system notifications signal when input is needed
- **5-10 web Claudes** at claude.ai/code, used in parallel with local

The mental model: context-switching is the bottleneck, not coding speed. You're "playing StarCraft with your codebase" — orchestrating multiple parallel workers and supervising their handoffs.

The "teleport" feature pushes a local session to web — useful for resuming work from a different device or the iOS app.

## Use Opus with Thinking

![Opus thinking mode](../../images/20260605-1722-boris-opus-thinking.png)

Boris's default: Opus with extended thinking for everything.

> "Even though it's bigger and slower than Sonnet, you have to steer it less."

Per-session it's slower; end-to-end it's faster because you spend less time correcting wrong assumptions. Particularly true for tool-heavy work (multi-file edits, code review, debugging).

## Prompt Engineering for Claude Code

The formula that works:

```
ACTION + TARGET FILE + TECH STACK + CONSTRAINTS + REFERENCE PATTERN
```

**Bad:** "Add a contact form."

**Good:** "Add a contact form in React with Zod validation in `src/components/ContactForm.tsx`, using strict TypeScript and Tailwind CSS, following the patterns in `src/components/LoginForm.tsx`."

Other principles:
- **One prompt, one task** — don't pile multiple unrelated changes into a single prompt
- **Reference existing patterns** — Claude is much better at matching than inventing
- **For complex tasks, ask Claude to ask you clarifying questions first** — the back-and-forth costs less than a wrong implementation

## Context Management

Symptoms of context decay:
- Claude ignores rules from CLAUDE.md
- Re-asks questions already answered
- Produces code that contradicts earlier code in the same session

Commands:

| Command | When | Effect |
|---|---|---|
| `/context` | Periodically during long sessions | Inspect what's currently in-window |
| `/compact` | After ~30 exchanges | Compresses history to ~60-70% of size |
| `/clear` | Switching tasks | Reset entirely |

**Cap MCP servers at 3-4.** Each MCP server adds ~20K tokens to the system prompt just from tool descriptions. Adding seven Slack-Jira-DB-CI-monitoring-search-docs MCPs eats your context window before the session starts.

**One objective per session.** When you switch tasks, `/clear`. Resist the temptation to "while we're here, also fix..."

## Slash Commands

Store in `.claude/commands/`. Type `/` to invoke. Examples that pay off:

- `/commit-push-pr` — full ship sequence in one keystroke
- `/add-blog` — repository-specific workflow
- `/review` — your team's PR review checklist as a workflow

Slash commands can include inline bash, file references, and parameterized arguments. Check them into Git so the whole team gets them.

## Custom Subagents

![Custom subagents](../../images/20260605-1727-boris-subagents.png)

Define specialized agents in `.claude/agents/` for repeated patterns:
- **Code Simplifier** — reviews diffs for over-engineering
- **Verify App** — runs the app and tests UI flows with browser tools
- **Security Reviewer** — known vulnerability patterns
- **Test Generator** — produces unit/integration tests from code

With **Agent Teams** (Opus 4.6+), define role-based agents that can call each other for complex workflows.

## Permissions over Dangerous-Skip

Use `/permissions` and check `.claude/settings.json` into Git rather than `--dangerously-skip-permissions`. Pre-allow common, safe bash commands; require explicit approval for risky ones.

Sharing permissions in Git means the team operates with the same safety boundaries.

### The 7 Permission Modes

![7 Permission Modes in Claude Code](../../images/20260621-1530-claude-code-permission-modes.png)

Claude Code has 7 named modes spanning a spectrum from full user oversight to near-autonomous execution. Only 5 are user-selectable; `auto` is feature-flag gated and `bubble` is internal.

| Mode | What the gate does | Who decides | Autonomy | User-selectable |
|---|---|---|---|---|
| `plan` | Model publishes a plan; nothing executes until user approves | User | LOW | Yes |
| `default` | Standard interactive use; approval needed for most tools | User | LOW-MED | Yes |
| `acceptEdits` | Auto-approves safe edits in the working directory; shell commands still prompt | Auto-classifier | MEDIUM | Yes |
| `auto` | ML classifier decides on requests that miss the fast path | Classifier | MEDIUM | No (feature flag) |
| `dontAsk` | No confirmation prompts shown; deny rules still enforced | Rules engine | HIGH | Yes |
| `bypassPermissions` | Skips most prompts; only safety-critical guards remain | System | MAX | Yes |
| `bubble` | Internal — subagent escalates permission request to its parent agent | Subagent | INTERNAL | No (runtime only) |

**When to use each:**
- `plan` — high-stakes or unfamiliar codebases; review intent before any changes
- `default` — normal development; oversight without requiring an explicit plan
- `acceptEdits` — active coding sessions; removes edit friction while keeping shell guardrails
- `dontAsk` — CI/CD pipelines or automated scripts with no human in the loop
- `bypassPermissions` — headless batch agents in controlled environments; establishes trust externally
- `bubble` — set automatically by the runtime when a subagent needs to escalate to its orchestrator

## MCP for Tools

Connect Slack, BigQuery, Sentry, GitHub, internal APIs via MCP servers. Configs go in Git so they're shared.

Two warnings:
- **MCPs eat context.** Each server's tool descriptions live in the system prompt. Aggressive cap: 3-4 servers active per session.
- **Prompt injection risk.** MCP responses become Claude's context. A malicious response can issue instructions. Apply least-privilege per MCP and review return values when bridging external systems. See [agent identity and auth](../concepts/agent-identity-and-auth.md) for the broader trust model.

## Verification Loops — The Highest-Leverage Tip

![Verification loop](../../images/20260605-1729-boris-verification-loop.png)

> "Give Claude a way to verify its own work" — claimed to 2-3x final output quality.

Concrete patterns:
- **Chrome extension / Playwright** — Claude opens the browser, exercises the UI, sees errors, iterates
- **Agent-stop hooks** — deterministic check runs on every agent stop (lint, type-check, smoke test)
- **Custom Verify-App subagent** — kicks off the app and tests it after major edits
- **Background validation agent** — long-running tasks finish with verification before declaring success

The pattern: any feedback loop that's **deterministic** beats any prompt instructing Claude to "verify your work."

## Primary Use Cases (Ranked by Value)

1. **Debugging** — paste raw errors, full stack traces, last 3 commit diffs. Most bugs resolve in <3 exchanges. Highest ROI use case.
2. **Onboarding to unfamiliar codebases** — 80% faster with solid CLAUDE.md. Have Claude explain modules instead of reading docs cold.
3. **Refactoring at scale** — Builder.io refactored an 18,000-line React component with cross-import renames. IDE tools can't compete at this scale.
4. **Test writing** — Claude matches your existing framework and patterns.

## Compared to Cursor and Copilot

| | Copilot | Cursor | Claude Code |
|---|---|---|---|
| **Context window** | ~8K | ~70-120K | **1M (Opus 4.6)** |
| **Mode** | Inline autocomplete | IDE-embedded chat + edits | Terminal agent |
| **Best for** | Typing assistance | Multi-file IDE edits | Complex multi-file agentic work |
| **Quality vs Cursor** | n/a | baseline | ~30% less rework reported |

Many devs run both: Claude Code for complex multi-file/agentic work, Copilot or Cursor for inline-while-typing help.

## Pricing (as of 2026)

| Plan | Price | Sufficient for |
|---|---|---|
| Free | $0 | Web chat only, no Claude Code |
| Pro | $20/mo | Daily individual use, ~10-45 prompts per 5-hour window |
| Max 5× | $100/mo | Heavy users, 1M-token Opus 4.6 |
| Max 20× | $200/mo | Highest priority, 20× capacity |

API users typically spend ~$6/day for heavy use; Max plans are often better value.

## Curating Standing Context — The Pruning Discipline

Every Claude Code session starts amnesiac. The fix isn't a better prompt; it's a **system that compounds** through three primitives:

| Primitive | What it solves |
|---|---|
| **CLAUDE.md (agent files)** | Loads conventions/layout/rules automatically each session. Hierarchical: personal (`~/.claude/CLAUDE.md`) + team + project. |
| **Skills** | Reusable multi-step procedures bundled with executable scripts. Converts manual re-explanation into invocation. |
| **Hooks** | Automated corrections at lifecycle points (PreToolUse, PostToolUse). Enforces formatting/checks without relying on model memory. |

Compounding context is the goal — but compounded context becomes **standing overhead** if you don't prune. A bloated CLAUDE.md or unused skill pack lives in the system prompt of *every* session.

### Measure with `/context`

Run `/context` to see the token allocation across categories. It exposes:
- System prompt size (fixed)
- CLAUDE.md cost (your responsibility)
- Active skills and their token cost
- MCP server descriptions
- Conversation history

Anything in "standing" categories that you don't actively use is **paying rent every session**.

### Prune aggressively

Concrete wins reported by practitioners:
- **CLAUDE.md from 3,500 → 1,200 tokens** by removing aspirational rules, duplicated style guidance, and never-triggered conventions
- **Removing 14.4k tokens of unused skill packs** via `/skills` (deactivate) and `/plugin` (uninstall)

Counter-intuitive principle: **clarity improves model performance**. A lean CLAUDE.md gets followed; a bloated one gets ignored or pattern-matched poorly. The same goes for skills — a directory of 30 skills competes for the model's attention; 5 well-scoped ones get used.

### Maintenance cadence

Treat the standing context like a dependency tree:
- Once a month, run `/context` and ask: what's here that I haven't used in the last 10 sessions?
- When you add a rule to CLAUDE.md, check whether a similar rule already exists (consolidate, don't accumulate)
- When you install a plugin, decide upfront whether it earns its token cost — and remove it if it doesn't

## Related

- [Claude Code architecture](claude-code-architecture.md) — internals
- [Claude Code: 12 features](claude-code-features.md) — feature reference
- [Claude Code vs OpenClaw](claude-code-vs-openclaw.md) — architectural comparison
- [AI-native engineering](../agent-teams-harness-eng/ai-native-engineering.md) — broader pattern of orchestrating AI agents
- [Context engineering](../concepts/context-engineering.md) — the discipline behind CLAUDE.md and `/compact`

---

## Optimize the CLI Environment, Not Just the Prompts

A useful framing: **treat Claude Code like a new engineer joining your team**. New engineers need optimized environments — good shell tooling, fast lookups, clear errors. Same for Claude Code. Pre-installing the right CLI tools makes its work materially better.

When asked to audit its own environment, Claude Code consistently flags these tools as missing-but-wanted:

| Tool | Why Claude Code wants it |
|---|---|
| **ripgrep (`rg`)** | Modern grep replacement; faster, respects `.gitignore` — what Claude wants to use for code search |
| **fd** | Modern `find` replacement; same speed/`.gitignore` benefits |
| **fzf** | Interactive filtering in pipes (`<some_cmd> \| fzf`) |
| **DuckDB** | Direct SQL on CSV / Parquet / JSON files without Python wrapping |
| **git-delta** | Structured, line-numbered diffs that Claude parses much better than raw `git diff` |
| **xh** | Structured-output HTTP client; cleaner than curl for inspection |
| **watchexec** | File-change monitoring — Claude can react to edits without manual prompting |
| **just** | Task runner Claude prefers over Makefiles (simpler grammar) |
| **semgrep** | Deterministic static analysis; lets Claude check for patterns rather than guess |

The author's framing: **"Claude doesn't actually have preferences"** — these recommendations emerge from what Claude's analysis features depend on. Installing them isn't just about making Claude happier; it's about giving its built-in tools the substrate they're optimized for.

The general principle: **invest in dev-environment ergonomics as a multiplier on Claude Code output**. The cost of `brew install` is small; the cumulative effect over a year of agentic coding sessions is large.

## Token Optimization Tools

Four complementary strategies can cut Claude Code token usage by up to 90%. Stack them additively — but each has conditions where disabling it preserves quality over efficiency.

| Strategy | Tool | Install | When ON | When OFF |
|---|---|---|---|---|
| Index code | `codegraph` | open-source | Large/unfamiliar codebases, exploration | After heavy refactors until re-synced (stale index = hallucinated functions) |
| Compress output | `RTK` (Rust Token Killer) | `brew install rtk` | Normal build/test cycles | Debugging or full-log analysis (lossy compression drops the needed line) |
| Trim responses | `caveman` | `npm install -g caveman` | Quick Q&A, simple one-off tasks | Long multi-step plan-mode sessions (thins session memory, degrades continuity) |
| Session hygiene | Built-in commands | always available | Always | N/A |

### codegraph — Index the Code

Parses your repo into a queryable graph of functions, classes, imports, and call relationships. Claude queries the graph in natural language instead of grepping files sequentially — eliminating large amounts of irrelevant file content from the context window.

![codegraph indexing demo](../../images/20260618-1510-codegraph-index-demo.gif)

**Risk:** the graph is a second source of truth that drifts. Stale indexes cause Claude to reference deleted functions or miss new ones. Re-sync after significant refactors.

### RTK — Compress the Output

Sits in front of commands and compresses their output before it reaches Claude. Example: 43 test results become "43 test paths"; warnings are bundled.

![RTK output compression demo](../../images/20260618-1511-rtk-output-compression-demo.gif)

**Risk:** compression is lossy — the dropped detail is sometimes the critical one. Disable during detailed debugging or when Claude needs to self-correct step-by-step.

### caveman — Make Claude Talk Less

Per-session mode switcher (light/full/ultra) that strips filler from Claude's responses. Ultra mode cuts length roughly in half at comparable quality.

![caveman response trimming demo](../../images/20260618-1512-caveman-response-trim-demo.gif)

**Risk:** Claude uses its own message history as session memory. Aggressive trimming degrades multi-step task quality by thinning that memory. Keep it off for plan-mode builds.

### Session Hygiene — The Free Layer

![Session management commands](../../images/20260618-1513-session-management-commands.png)

Built-in commands always available:
- `/context` — audit what's consuming the window (a 50k-token CLAUDE.md burns ~4% before you type anything)
- `/clear` — reset between unrelated tasks
- `/compact` — summarize and continue
- `/model` — pick Haiku/Sonnet/Opus by task type

Model selection heuristic: Haiku for speed-critical automation; Sonnet for repeatable scheduled tasks; most capable model for planning and deep work. Downgrading to a cheaper model and getting a poor result costs more tokens in redo than staying on the capable model.

Additional high-leverage habits: use Plan Mode before edits to eliminate expensive trial-and-error loops; sketch UI designs first rather than having Claude code cold.

> "I've seen a 50k-token CLAUDE.md quietly burn about 4% of the window every single time you open a session, before you've even typed anything."

> "The skill isn't installing these. It's knowing which one to turn on for the task in front of you, and which to leave off."

---

**Source:** https://ainative.to/p/how-to-use-claude-code-beginners-guide
**Source:** https://getpushtoprod.substack.com/p/how-the-creator-of-claude-code-actually
**Source:** https://sderosiaux.substack.com/p/claude-code-told-me-what-tools-it
**Source:** https://aiagentssimplified.substack.com/p/the-hidden-cost-of-starting-from
**Source:** https://getpushtoprod.substack.com/p/how-to-reduce-90-of-claude-code-token
**Date:** 2026-06-18 (token optimization section added; context section 2026-06-16; initial 2026-06-05)
**Tags:** claude-code, workflow, plan-mode, claude-md, mcp, slash-commands, subagents, parallel-agents, compounding-engineering, opus, verification-loops, cli-tools, ripgrep, duckdb, environment, context-pruning, skills, hooks, session-amnesia, token-optimization, codegraph, rtk, caveman, cost-reduction
