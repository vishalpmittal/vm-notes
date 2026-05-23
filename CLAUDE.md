# VM Notes - Claude Instructions

@BEHAVIORAL-GUIDELINES.md

## Repository Purpose

Personal knowledge base of technical notes, learnings, and curated blog content. Topics span software engineering, system design, AI/ML, and emerging technologies. Notes are primarily markdown files, often sourced from online articles for future reference.

## Workflow Guidelines

### Adding Notes
- All notes go in markdown files organized under topic directories (e.g., `ai/`, `design/`, `backend/`, `devops/`)
- When adding a note from an online source, include the source URL and date at the top as YAML frontmatter:
  ```yaml
  ---
  title: "Article Title"
  source: https://example.com/article
  date: 2026-05-23
  tags: [topic1, topic2]
  ---
  ```
- Keep the original content intact but reformat for readability when needed
- Extract key takeaways into a `## Key Takeaways` section at the top when summarizing

### Deduplication
- Before adding a new note, search existing files for overlapping content (`grep -ri` for key terms)
- When duplicates exist, merge into a single authoritative note per topic with sections attributed to sources
- Flag near-duplicates to the user rather than silently merging

### Organization
- Top-level directories are topic buckets. Current convention:
  - Keep directory names lowercase, hyphenated (e.g., `system-design/`, `distributed-systems/`)
  - Use an `_index.md` in each directory as a table of contents for that bucket
- When reorganizing, update all cross-references and the root `_index.md`

### Images
- Store all images in the top-level `images/` directory (not inside topic folders)
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
