# VM Notes - Claude Instructions

@BEHAVIORAL-GUIDELINES.md

## Repository Purpose

Personal knowledge base of technical notes, learnings, and curated blog content. Topics span software engineering, system design, AI/ML, and emerging technologies. Notes are primarily markdown files, often sourced from online articles for future reference.

## Workflow Guidelines

### Adding Notes
- All notes live under the `notes/` directory — topic folders and images are inside it
- Each topic gets ONE file — do not create a separate file per blog post
- Before creating a new file, search existing notes for related content (`grep -ri` for key terms)
- If a related note exists, append new content as a new section within that file and merge overlapping parts
- Only create a new file when no existing note covers the topic
- Use `/add-blog <url>` to automate the full workflow (fetch, clean, categorize, merge, save)
- When adding from an online source, include source attribution in YAML frontmatter:
  ```yaml
  ---
  title: "Topic Name"
  source: https://example.com/article
  date: 2026-05-23
  tags: [topic1, topic2]
  ---
  ```
- For files with multiple sources, use a `sources:` list:
  ```yaml
  sources:
    - https://first-article.com
    - https://second-article.com
  ```
- Extract key takeaways into a `## Key Takeaways` section at the top
- Filenames should reflect the broad topic, not a specific article title

### Deduplication & Merging
- Always search before adding — one authoritative file per topic
- When merging into an existing note, enrich existing sections rather than duplicating content
- Update the Key Takeaways section when adding new insights
- Update tags to reflect the combined content

### Organization
- All content lives under `notes/` — topic folders and `images/` are inside it
- Up to two levels of folders allowed, then markdown files (e.g., `notes/ai-concepts/llm/prompting.md`)
- No deeper nesting — max depth is `notes/<category>/<subcategory>/<file>.md`
- Keep directory names lowercase, hyphenated
- Use an `_index.md` in each directory as a table of contents for that bucket
- When reorganizing, update all cross-references and the root `_index.md`

### Images
- Store all images in `notes/images/` (not inside topic folders)
- Name format: `yyyymmdd-hhmm-image-description.png` (e.g., `20260523-1430-claude-context-flow.png`)
- Reference from markdown files using relative paths: `![description](../images/20260523-1430-claude-context-flow.png)`
- Keep images at a readable resolution but optimize file size (compress PNGs/JPGs before committing)

### Static Site Generation
- This repo is intended to produce a browsable documentation site (static HTML)
- When the site tooling is set up, respect its config and directory conventions
- Generated/output files should be gitignored, not committed

## Code Style

- Markdown: ATX-style headers (`#`), one blank line between sections
- Filenames: lowercase, hyphenated (e.g., `cap-theorem-notes.md`)
- No trailing whitespace; files end with a single newline

## What Not to Do

- Don't delete or overwrite existing notes without confirmation
- Don't reorganize the entire directory structure unprompted — propose the new structure first
- Don't commit generated site output
- Don't strip source attribution from curated content
