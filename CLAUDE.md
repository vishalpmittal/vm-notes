# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@BEHAVIORAL-GUIDELINES.md

## Repository Purpose

Personal knowledge base of technical notes and curated blog content. Topics: software engineering, system design, AI/ML, and emerging technologies. Notes are markdown files, often sourced from online articles.

## Commands

- `/add-blog <url> [<url> ...]` — fetch a blog post, clean ads, download images, auto-categorize, merge with existing notes, and print a summary. For 2+ URLs, spawns parallel worker agents per blog plus a consolidation scout to map existing internal content.
- `/course-notes <course-file> <text|url|image>` — append concise notes to a course file and update the directory's glossary.md with key terms

## Agents

- `leadership-coach` (`.claude/agents/leadership-coach.md`) — engineering leadership coaching for Sr Manager / Director situations (difficult engineer behavior, exec dynamics, delegation, hiring). Grounds advice in `notes/leadership/` and writes structured coaching plans into `notes/leadership/coaching-cases/`.

## Architecture

```
notes/                          # all content lives here
├── images/                     # centralized image storage (flat)
├── ai-ml-ds/                   # AI, ML, data science
│   ├── agents/                 # agent frameworks, real-world agent writeups
│   ├── claude/                 # Claude Code, Anthropic tools
│   ├── concepts/               # RAG, LLM patterns, writing with LLMs
│   └── inference/              # ML serving, inference at scale
├── system-design/              # architecture, distributed systems
├── design/                     # product design, UX, design systems
├── infra/                      # DevOps, CI/CD, cloud, containers
├── courses/                    # course notes (see Courses below)
│   └── <course-name>/          # one subfolder per course
├── leadership/                 # management, leadership (summaries only)
│   └── coaching-cases/         # leadership-coach agent output (see Agents)
├── life-coaching/              # life advice, personal growth
├── dad-jokes/                  # humor
└── <new-categories-as-needed>/
```

- One markdown file per topic — multiple blog sources merge into a single note
- Max folder depth: `notes/<category>/<subcategory>/<file>.md` (two levels, then files)
- `notes/images/` is flat — all images regardless of topic category

## Adding Notes

Search before creating: `grep -ri "key terms" notes/ --include="*.md" -l`
- If a related note exists → append and merge, don't create a new file
- If multiple placement options → prompt the user to choose
- If no related note exists → create in the best-fit category directory

### Note Format

Each note starts with a `# Title` heading, then `## Key Takeaways` (3-5 bullets). Metadata goes at the end after a horizontal rule:

```markdown
# Topic Name

## Key Takeaways
...

## Content sections
...

---

**Source:** https://example.com/article
**Date:** 2026-05-23
**Tags:** topic1, topic2
```

Multiple sources use multiple **Source:** lines. Filenames reflect the broad topic, not a specific article title.

## Courses

Course notes live in `notes/courses/<course-name>/` with a per-directory `glossary.md` table. The `/course-notes` command appends sections to a course file and auto-updates the glossary.

## Images

- Location: `notes/images/` (never inside topic folders)
- Naming: `yyyymmdd-hhmm-description.ext` (e.g., `20260523-1430-claude-context-flow.png`)
- Reference: `![alt](../images/20260523-1430-claude-context-flow.png)` (relative from topic folder)
- Optimize file size; keep readable resolution

## Style

- ATX headers (`#`), one blank line between sections
- Filenames: lowercase, hyphenated
- No trailing whitespace; single trailing newline

## Constraints

- Never delete or overwrite notes without confirmation
- Never reorganize directory structure unprompted — propose first
- Never strip source attribution
- Never commit generated site output (future static site build artifacts should be gitignored)
