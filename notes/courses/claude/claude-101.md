# Claude 101

## Key Takeaways

- Claude is built on three core principles: helpful, harmless, and honest
- Trained using Constitutional AI (CAI) — aligned with human values and operates transparently
- Designed to avoid toxic/discriminatory outputs and refuse assistance with illegal or unethical activities

## 4D Framework for AI Fluency

- **Delegation** — decide what humans vs. AI should do; understand AI capabilities, distribute tasks strategically
- **Description** — communicate effectively with AI; define outputs, guide processes, specify desired behaviors
- **Discernment** — critically evaluate AI outputs for quality, accuracy, and appropriateness; identify improvements
- **Diligence** — use AI responsibly; maintain transparency, make ethical choices, take accountability for AI-assisted work

## Skills

- Folders of instructions, scripts, and resources that Claude loads dynamically
- Act as expertise packages — teach Claude how to complete specific tasks repeatably

### Folder Structure

```
my-skill/
├── SKILL.md       # the instruction file — when to use, what to do
├── scripts/       # executable helpers the skill can shell out to
├── references/    # background docs Claude can read on demand
└── assets/        # templates, examples, fixed files used by the skill
```

`SKILL.md` is the entry point — everything else is loaded only when the skill is invoked.

## Prompts — Essential Parts

A useful prompt is structured, not just typed. Six parts to consider on any non-trivial prompt:

1. **Role** — who Claude is acting as (e.g., "You are a senior backend engineer reviewing a migration")
2. **Context** — background it needs that's not in the data (project goals, audience, constraints)
3. **Data description** — what the inputs are and how to read them (schemas, file structure, units)
4. **Task description** — the actual ask, stated precisely
5. **Example** — one or more shows of the desired behavior (few-shot)
6. **Output format** — the exact shape of the answer (JSON schema, table, bulleted list, length cap)

Skipping any of these is a deliberate choice — not skipping them by accident is the point of having the checklist.

## Skills vs Projects

- **Projects** store knowledge; **Skills** perform tasks
- Projects = knowledge hubs (specs, notes, research) — context persists across all chats in the project
- Skills = procedural machines (steps, order, methodology) — instructions applied when invoked
- They complement each other: skills can reference knowledge stored in projects (what + how)

| | Projects | Skills |
|---|---|---|
| Purpose | Store knowledge | Define processes |
| Best for | Long-term context, reference materials | Repeatable workflows, multi-step tasks |
| Example | Customer hub, research buddy | Brand guidelines, blog drafting |
| Persistence | All chats in project | When invoked |

## Connectors

- Give Claude access to your tools, data, and context — turns it from assistant into informed collaborator
- Can read info and perform actions: search files, retrieve docs, analyze data, create content, update records
- Powered by **Model Context Protocol (MCP)** — universal standard like USB-C for AI; one interface, many apps
- Two types:
  - **Web connectors** — link to cloud services (Google Drive, Notion, Slack, Asana)
  - **Desktop extensions** — run locally via Claude Desktop app; access local files and native applications

---

**Date:** 2026-05-25, updated 2026-06-17 (skill folder structure + prompts 6-part formula)
**Source:** /Users/vimittal/Downloads/prep/prep.html (Claude section)
**Tags:** claude, constitutional-ai, ai-safety, anthropic, 4d-framework, ai-fluency, skills, projects, connectors, mcp, prompt-structure, skill-md
