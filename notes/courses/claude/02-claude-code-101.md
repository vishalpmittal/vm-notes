# Claude Code 101

Anthropic Academy course covering the Claude Code CLI from install to advanced customization (hooks, subagents, MCP). 13 lessons consolidated.

## Executive Summary

- **Claude Code is agentic, not a chat box.** It reads your codebase, edits files, runs commands, and verifies its own work via an agentic loop (gather context → take action → verify → repeat) — that's what separates it from Claude.ai
- **The core workflow is Explore → Plan → Code → Commit.** Use Plan Mode (`Shift+Tab`) for the first two steps so Claude can read and reason before touching any files — most user pain comes from skipping straight to "Code"
- **Context is finite and is the real resource you're managing.** `/context` shows what's eating it, `/compact` summarizes, `/clear` resets. Subagents, Skills (lazy-load), and CLI-equivalent tools all reduce context cost vs MCP servers (which preload all tool definitions)
- **CLAUDE.md is persistent project memory.** Auto-read every session, committed to version control, hierarchical (project + user level). Start without one and add rules as you find yourself correcting Claude — keeps it compact
- **Customization layers stack from suggestion → guarantee.** CLAUDE.md (read every session), subagents (isolated context), Skills (lazy-loaded procedures), MCP (external tools/data), Hooks (deterministic, *always* run). For "must happen every time," use a hook, not a prompt

---

## 1. Foundations

### What Claude Code is

- Agentic coding tool — direct access to files, terminal, full codebase (vs Claude.ai which only sees what you paste)
- Available in terminal, VS Code, JetBrains, Claude Desktop, and web (`claude.ai/code`)
- Capabilities: read/understand codebase, edit files across project, run terminal commands, search the web

### What an AI agent is

- Software that interacts with its environment and takes actions to complete a defined goal
- Powered by an LLM running in a real-time loop with access to tools, services, or other agents

### The agentic loop

1. You enter a prompt
2. Claude gathers context (model returns text or tool calls)
3. It takes action (edit a file, run a command)
4. It verifies the results against your goal
5. If complete → wait for next prompt; if not → loop back

You can interrupt, steer, or add context at any point in the loop.

### Three things to keep in mind

- **Context window** — finite working memory; Claude finds info strategically rather than loading the whole codebase
- **Permissions** — asks before running commands / editing files by default; configurable
- **It can make mistakes** — misunderstand intent, introduce bugs, over-engineer; stay in the loop

---

## 2. Installing

| Platform | Install method |
|---|---|
| macOS / Linux / WSL | `curl` one-liner (recommended — supports auto-update). `brew install` works but no auto-update |
| Windows | `Invoke-RestMethod` (PowerShell), `curl` (CMD), or `winget` (no auto-update) |
| VS Code | Extensions panel → "Claude Code" by Anthropic (blue check) → install → restart |
| JetBrains | JetBrains Marketplace → "Claude Code" plugin → restart IDE |
| Claude Desktop | Toggle "Code" at top after signing in — works on a specific folder, can run in cloud |
| Web | `claude.ai/code` — restricted to GitHub repos |

- After install, run `claude` in your project root — initial setup picks color theme, login (Pro/Max/Enterprise or API key)
- **Terminal gets features first**; IDE integrations mirror it closely. Desktop is good for background runs; web is good for remote work on GitHub repos
- Claude has access to the directory you run it in *and all subfolders*

---

## 3. Permission Modes & Plan Mode

`Shift+Tab` cycles between modes:

- **Approval (default)** — asks permission for every file edit and every command
- **Auto-accept** — file edits auto-approved; commands still need approval
- **Plan Mode** — read-only tools; Claude analyzes codebase and returns a detailed plan without writing anything

Plan Mode is the cornerstone of the recommended workflow — use it for any non-trivial change.

---

## 4. Daily Workflow: Explore → Plan → Code → Commit

> *"If you take one thing away from this course, let it be this workflow."*

### Explore + Plan (use Plan Mode)

- `Shift+Tab` to Plan Mode → write a detailed prompt (e.g., "Add WebP conversion to our image upload pipeline — figure out where in the pipeline, whether we need new dependencies, and the approach")
- Claude reads files, runs web searches, returns a plan
- Best place to course-correct — *before* code is written. Ask Claude to revise specific areas if needed
- You can also run the explore subagent outside Plan Mode for a general codebase summary

### Code

- Approve the plan → Claude works through it (auto-accept or per-step approval)
- Tips:
  - **Define success criteria explicitly** — Claude needs to know what "correct" looks like
  - **Add tools** — e.g., Claude in Chrome extension for browser-based UI testing
  - **Include a test suite** — Claude can validate against it; can even write tests for you
  - **Save recurring fixes to CLAUDE.md** — if Claude keeps hitting the same issue, ask it to save the solution

### Commit

- Run a **subagent code reviewer** before pushing (fresh eyes, no session bias)
- Have Claude generate the commit message in your style
- Use `/commit-push-pr` skill to handle commit + push + PR creation in one step
- If a Slack MCP server is configured with channels in your CLAUDE.md, the PR link auto-posts to your team channel
- **Session linking** — when Claude creates a PR via `gh pr create`, the session links to it; resume later with `claude --from-pr <PR_NUMBER>`

---

## 5. Context Management

### Mental model

Context window = Claude's working memory. Every prompt, file read, tool call, and result consumes space. When full, Claude **compacts** automatically — summarizes important details and drops unnecessary tool results (can lose detail).

### Commands

| Command | What it does | When to use |
|---|---|---|
| `/compact` | Summarizes everything so far, frees space, keeps memory | Mid-feature, hitting limit but want to continue |
| `/clear` | Wipes everything — fresh session | Starting a new feature; avoid bias from prior work |
| `/context` | Shows context size breakdown (categories + visual bar chart) | Diagnose what's eating context |

### Tips for saving context

- **Be specific** — vague prompts cost more long-term because Claude has to explore and reason more
- **Manage MCP servers** — they preload all tool definitions even when unused. Disable unrelated ones. Skills are leaner alternatives (lazy-loaded)
- **Use subagents** — isolated context window; return just the answer (e.g., "where are the auth endpoints?")
- **Persistent rules belong in CLAUDE.md**, not the conversation — so Claude doesn't rediscover them next session

---

## 6. Code Review Features

- **Subagent code reviewer** — restrict to read-only tools (flag issues, don't edit); check the agent config into the repo so the whole team uses the same reviewer
- **`/commit-push-pr` skill** — one command for commit + push + PR creation; integrates with Slack MCP for auto-posting
- **`claude --from-pr <N>`** — resume the session originally linked to a PR (for addressing review comments or fixing CI later)

---

## 7. CLAUDE.md — Persistent Project Memory

### Why it exists

Without CLAUDE.md, Claude starts fresh every session — re-explores the codebase, guesses dependencies, misses conventions. CLAUDE.md is an onboarding script auto-appended to every prompt.

### Example structure

```markdown
# Project
This is a Next.js 15 app using App Router, Tailwind, Drizzle ORM.

# Commands
- Dev server: `pnpm dev`
- Run tests: `pnpm test`
- Lint: `pnpm lint`

# Code Style
- 2-space indentation
- Prefer named exports
- All API routes in app/api/
- Use server actions instead of API routes where possible
```

### Hierarchy

- **Project-level** — in repo root, committed, shared with team
- **User-level** — in your config folder, applies across all your projects, personal preferences

### Tips

- **Start without one** — see where you keep correcting Claude → those corrections become the CLAUDE.md. Keeps it compact and necessary
- **Save corrections to memory** — explicitly ask Claude to save a rule when you find yourself repeating it
- **Reference docs with `@filepath`** — e.g., `Please read: @README.md`
- **`/init`** — have Claude generate one for you when ready

---

## 8. Subagents

- Spawn a subagent for exploration/research tasks — runs in **its own isolated context window**, returns a summary, keeps the main context clean
- Especially useful for "where is X?" questions where you only need the answer, not the journey

### Creating one

- `/agents` → "Create new agent" — walks you through scope, purpose, tools, color
- Defined in Markdown files with YAML frontmatter; Claude generates name, description, prompt
- Claude uses the description to decide when to call the subagent automatically

### Customization

- **Persistent memory** — subagent retains memory across conversations (good for project-specific subagents used repeatedly)
- **Preload skills** — add `skill` key listing skill names; note that unlike main-conversation skills, the *entire* skill is loaded into the subagent's context

---

## 9. Skills

Course defers to dedicated [Introduction to Agent Skills](https://anthropic.skilljar.com/introduction-to-agent-skills) course. Practical takeaway from elsewhere in this course: Skills are leaner than MCP because they're **lazy-loaded** — only the name and description sit in context until Claude decides to invoke.

See also: [claude-101.md#skills](claude-101.md#skills) (skill folder structure: `SKILL.md`, `scripts/`, `references/`, `assets/`).

---

## 10. MCP (Model Context Protocol)

### What it is

Open standard for connecting Claude Code to external tools and data sources. Bridges the gap between your codebase and external context (databases, productivity apps, public repos, docs).

### Use cases

- **Linear MCP** — pull issue details into Claude's context
- **Context7 MCP** — fetch up-to-date dependency docs
- Any tool your team uses that you'd otherwise paste from

### Adding servers

`claude mcp add <name> ...`

Two server types:

- **HTTP** — remote, network-connected (hosted by service provider)
- **Stdio** — local process on your machine

Manage with `/mcp` inside a session (status, disable, view tools).

### Scoping

| Scope | Available |
|---|---|
| **Local** | This project, just you |
| **User** | All your projects |
| **Project** | Via `.mcp.json` checked into VCS — entire team gets identical servers automatically |

### Context cost (important)

- **MCP tool definitions load into context whether you use them or not** — every active server pays rent
- Disable unused servers with `/mcp`
- **Prefer CLI over MCP** when an equivalent exists (`gh`, `aws`) — no persistent tool definitions in context
- **Prefer Skills over MCP** when possible — only name + description loaded upfront; full body loaded on demand
- If MCP tools exceed **10% of context window**, Claude Code auto-switches to **tool search mode** (discovers tools on demand — less reliable)

---

## 11. Hooks

### The deterministic guarantee

The key difference between hooks and everything else: **hooks always run.** Telling Claude in CLAUDE.md to run Prettier after every edit works *most* of the time — a hook makes it happen *every* time.

### Common use cases

- Auto-formatting after file edits
- Logging executed commands for compliance
- Blocking dangerous operations (e.g., writes to production config)
- Notifications when Claude finishes a task

### Configuration

Configured in `settings.json` (or via `/hooks` inside Claude Code). Each hook = event + optional matcher (which tools) + command.

### Events

| Event | Fires |
|---|---|
| **PreToolUse** | Before a tool call (can *block*) |
| **PostToolUse** | After a tool call completes |
| **UserPromptSubmit** | When you submit a prompt, before Claude processes it |
| **Stop** | When Claude finishes responding |
| **Notification** | When Claude sends a notification |

### Auto-format example

PostToolUse hook with matcher `"Edit|MultiEdit|Write"` → command checks file extension → runs Prettier / gofmt / etc.

### Blocking with PreToolUse

Hook receives tool name + input as JSON on stdin. Exit codes:

- **0** — proceed normally
- **2** — block; stderr message fed back to Claude as feedback so it knows why and can adjust
- **anything else** — non-blocking error (shown to you, doesn't stop)

Use for hard rules: block writes to prod config, block `rm -rf`, block commits to main.

### Sharing with the team

- Hooks in `.claude/settings.json` are project-level — commit them to the repo
- Use `CLAUDE_PROJECT_DIR` env var in commands to reference project-stored scripts regardless of Claude's CWD

### Rule of thumb

> If something needs to happen every time without fail, don't put it in a prompt. Put it in a hook.

---

## Quiz Recap (Key Concepts to Remember)

1. **What is an AI agent?** → AI that takes action to complete goals (not just chat)
2. **What happens at context limit?** → Auto-compaction summarizes the conversation to free space
3. **Recommended workflow?** → Explore → Plan → Code → Commit
4. **How does Claude Code use CLAUDE.md?** → Reads it automatically at the start of every session

---

## See Also

- [claude-code-architecture.md](../../ai-ml-ds/claude/claude-code-architecture.md) — internals
- [claude-code-workflow.md](../../ai-ml-ds/claude/claude-code-workflow.md) — practitioner workflow patterns, context pruning, CLI tool ideas
- [claude-101.md](claude-101.md) — sibling course on Claude (AI assistant fluency / 4D framework)

---

**Source:** Anthropic Academy — [Claude Code 101](https://anthropic.skilljar.com/claude-code-101) (13 lessons)
**Date:** 2026-06-17
**Tags:** claude-code, course, agentic-loop, plan-mode, context-management, claude-md, subagents, skills, mcp, hooks, code-review, explore-plan-code-commit, deterministic-control
