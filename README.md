# vm-notes

Personal knowledge base of technical notes, learnings, and curated blog content. Topics include software engineering, system design, AI/ML, and emerging technologies.

## Usage

Use `/add-blog <url>` to add a blog post. It will fetch the content, download images, auto-categorize into the right topic folder, and merge with existing notes when relevant.

## Structure

```
notes/
├── images/          # all diagrams and illustrations (yyyymmdd-hhmm-description.ext)
├── ai-concepts/     # AI/ML, LLMs, RAG, agents
├── claude/          # Claude Code, Anthropic tools
├── system-design/   # architecture, distributed systems
└── ...              # new topic folders as needed
```

One markdown file per topic (not per article) — multiple sources merge into a single note.
