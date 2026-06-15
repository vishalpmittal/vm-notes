---
description: Import personal notes from local docs, folders, pasted content, or URLs — categorize, dedupe, and divert TO-DO items to notes/to-do.md
argument-hint: <file-path> | <folder-path> | <url> | <pasted-content> [...]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, WebFetch, Agent
---

You are importing personal notes into this repository. The input is: $ARGUMENTS

Unlike `/add-blog`, this command imports **your own existing notes** from external docs. The output is the same: well-categorized markdown files in `notes/<category>/`, images in `notes/images/`, and consolidated TO-DO items in a single root `notes/to-do.md`.

---

## Decide the flow

- **Single doc** (one file, one URL, or one pasted blob) → follow Steps 1–7 in your own context
- **Multiple docs / folder** (2+ files or a folder path) → use the **Parallel Import Strategy** below, then Step 7 to summarize

## Input types

| Input | How to load |
|---|---|
| Local file path (`.md`, `.txt`, `.docx`, `.pdf`, etc.) | `Read` (or `Bash` for unsupported formats — `pandoc`, `docx2txt`, etc.) |
| Folder path | `Glob` to enumerate, then process each file |
| URL (Google Docs, Notion, public page) | `WebFetch` — warn user if auth-required; suggest export-and-pass-file instead |
| Pasted content | Already in `$ARGUMENTS` — parse directly |

---

## Parallel Import Strategy (use when 2+ docs)

Mirrors `/add-blog`'s multi-blog flow — protects your context window and parallelizes I/O.

### Role A — Worker agents (1 per doc, run in parallel)

Spawn one `general-purpose` Agent per source doc using `run_in_background: true`. Each worker:

1. Loads the source doc (`Read` for files; `WebFetch` for URLs; pasted content is handed over directly)
2. Copies meaningful images to `notes/images/` (apply the time-slot convention: assign each worker a non-overlapping 10-minute slot for filenames so they don't collide)
3. Returns a **structured report** — does NOT write any markdown files

The worker prompt must include the doc identifier, time-slot range, and the report template below. Tell the worker explicitly: *"Do NOT create or modify any markdown files. The supervisor will do that."*

**Worker report template:**

```
## DOC: <title or filename>
### Source: <file-path | url | "pasted-content-<n>">
### Doc Type: <technical | leadership | mixed>
### Topic Tags: [tag, tag, ...]
### Key Takeaways (3-5 bullets)
### Main Content Sections (heading → 2-4 sentence summary, preserve concrete details, code, numbers, examples)
### Notable Quotes / Definitions (max 3)
### Images Copied (filename — 1-line description, or "None")
### TO-DO Items Extracted (list every item verbatim, with the source heading it came from, or "None")
### Suggested Note Placement (existing files in notes/ this might merge into; or "new file: <category>/<filename>.md")
```

### Role B — Consolidation-scout agent (parallel with workers)

Spawn ONE `Explore` agent in parallel with the workers. Its job: map existing internal content in the directories likely to be relevant to the batch (inferred from doc topic), and read each candidate file's `## Key Takeaways` so the supervisor can decide append vs. create without re-exploring.

### Role C — Supervisor (you, the parent)

Once all workers + scout return:

1. **Cross-doc dedup** — read all worker reports together. If two source docs cover the same topic, plan a single merged note rather than two duplicates
2. **Match against scout's map** — for each doc, decide:
   - **Append** to an existing file (use `Edit`) when scope overlaps
   - **Create new** file (use `Write`) when no existing file matches
   - **Skip duplicate content** — if a source repeats what's already in a file, only add the new substance
3. **Preview copied images** with `Read` when worker captions were vague (e.g., "diagram2"). Rename with `mv` if needed. Delete page chrome / screenshots that turned out not to be useful
4. **Apply leadership formatting** (Key Takeaways + Actionable Insights, bullets not prose) for any doc tagged leadership
5. **Consolidate TO-DO items** from all workers into a single `notes/to-do.md` update (Step 6 below)
6. **Write all final notes** — do not delegate writing back to subagents

### Image-naming time-slot table (default)

Use today's date. Assign before spawning workers:

| Worker | Image time-slot range |
|---|---|
| 1 | `HHMM = 1500–1509` |
| 2 | `HHMM = 1510–1519` |
| 3 | `HHMM = 1520–1529` |
| ... | (continue in 10-min blocks) |

---

# Single-doc flow

## Step 1: Load Content

Load the source doc:

- **Local file** — use `Read`. For non-text formats, convert first: `pandoc input.docx -o /tmp/converted.md`, `pdftotext`, `docx2txt`, etc. Ask the user before installing tools
- **URL** — `WebFetch`. If it's a Google Doc / Notion link that returns auth walls or empty content, stop and ask the user to export to a local file
- **Pasted content** — it's already in `$ARGUMENTS`; parse directly
- **Folder** — `Glob '<folder>/**/*.{md,txt,docx}'` then process each (switch to Parallel Import Strategy)

Strip from the loaded content:
- Doc-tool chrome (Google Docs toolbar text, page numbers, "Last edited by ..." footers)
- Empty sections, accidental duplicate paragraphs from copy-paste
- Personal notes-to-self that aren't part of the technical content
- Comments / suggestion markers

**Preserve** all technical substance, code, examples, links, and especially anything in the TO-DO section.

## Step 2: Extract & Copy Images

For each meaningful image referenced in the source doc:

1. **Locate the source image file.** Common patterns:
   - Same folder as the doc (`./images/`, `./attachments/`)
   - Sibling folder (`../images/`)
   - Embedded in `.docx` (extract with `unzip docx -d /tmp/extracted` then look in `word/media/`)
   - Linked URL (download with `curl`)
2. **Copy into `notes/images/`** with the naming convention `yyyymmdd-hhmm-short-description.ext`
3. **Verify the copy succeeded** with `Read` on the new path

Skip:
- Logos, avatars, author photos
- Decorative separators, icons
- Anything <5KB (likely an icon)
- Stock photos (illustration of a person at a desk, etc.) — keep only diagrams, charts, infographics, screenshots that illustrate a concept

## Step 3: Extract the TO-DO Section

**Identify the TO-DO portion of the doc using either signal:**

1. **Heading match** — any heading (any level) whose text matches `/(?i)\b(to[\s-]?do|todo)\b/`. Examples that match: `## TODO`, `### To Do`, `## To-Do List`, `## TODO: things to study`. Items captured = everything until the next same-or-higher heading, or EOF
2. **Trailing convention** — if no TO-DO heading found, check whether content after the final `---` horizontal rule or after the last H2 in the doc looks like a TO-DO list (bullets that name topics without explaining them)

For each item extracted:
- Preserve the original wording
- Note any short context the user wrote next to the item
- Hold for Step 6 (do NOT write to `notes/to-do.md` yet — the supervisor in multi-doc mode batches all items first)

**Do not include TO-DO items in the main imported notes.** They live only in `notes/to-do.md`.

## Step 4: Find Existing Content & Categorize

First, list existing topic directories:

```
ls -d notes/*/
```

Then search for existing notes covering related topics:

```
grep -ri "key terms from the doc" notes/ --include="*.md" -l
```

Outcomes (same as `/add-blog`):

- **Multiple related notes** → present options to user with `AskUserQuestion`, don't pick silently
- **Exactly one related note** → `Read` it, then `Edit` to APPEND new substance under a clear section header, update **Source:** and **Tags:** at the bottom, dedupe against existing content
- **No related note** → create a new file in the best-fit category. Reuse the same category mapping `/add-blog` uses:
  - AI/ML, LLMs, prompting, RAG, agents → `notes/ai-ml-ds/concepts/` or `notes/ai-ml-ds/agents/`
  - Claude Code, Claude features → `notes/ai-ml-ds/claude/`
  - System design, distributed systems, databases → `notes/system-design/`
  - DevOps, CI/CD, infra → `notes/infra/`
  - Leadership, management → `notes/leadership/`
  - Life coaching, personal growth → `notes/life-coaching/`
  - Product, design, programming → `notes/product/`, `notes/design/`, `notes/programming/`
- Max folder depth: `notes/<category>/<subcategory>/<file>.md`

## Step 5: Write or Update the Note

**Preserve as much relevant information as possible, but cut the irrelevant.** Trade-off rules:

- Keep all concrete facts, frameworks, numbers, examples, code, diagrams
- Cut stream-of-consciousness asides, "I should research this later" parenthetical (those become TO-DO items in Step 6)
- Cut anything that duplicates what's already in the target file (the merge step)
- For **leadership** content — collapse to Key Takeaways + Actionable Insights, bullets not prose (same rule as `/add-blog`)

### New note format

Same as `/add-blog`: title H1, `## Key Takeaways`, content with relative image refs (`![alt](../images/<filename>)`), then metadata at the bottom:

```markdown
---

**Source:** <file-path | url | "Imported from pasted notes">
**Date:** <today YYYY-MM-DD>
**Tags:** topic1, topic2, topic3
```

### Updated note format

Append a new section under a clear header. Add a new `**Source:**` line (multi-source format). Update Key Takeaways and Tags if new substance warrants it. Dedupe — if the imported content overlaps existing sections, enrich rather than repeat.

## Step 6: Update `notes/to-do.md`

This is the **single root TO-DO file** for the whole repo, categorized by H2.

### If `notes/to-do.md` does NOT exist yet

Create it with this skeleton:

```markdown
# TODO — Topics to Study

Items collected from imported docs that aren't yet covered in this repo. Categorized by area.

> Format: `- [ ] **Topic** — short note on what to cover`. Add `**target file:**` once decided.

## System Design

## AI/ML & Agents

## Leadership

## Infra & DevOps

## Programming

## Product & Design

## Life Coaching

## Other
```

### Adding items

For each TO-DO item extracted in Step 3:

1. **Pick the right category** — match against the H2 sections above. If the item's topic spans two, file under the dominant one and reference the other inline
2. **Dedupe** — `grep` the existing `notes/to-do.md` for the topic. If already present, skip or enrich the existing entry with extra context the new doc provided
3. **Cross-check existing notes** — `grep -ril "topic" notes/ --include="*.md"` to confirm there isn't already a note covering this. If there is, the TO-DO item is stale; don't add it (mention in the summary)
4. Use the convention: `- [ ] **Topic Name** — what to cover (~1 sentence)`
5. Preserve any short context the user wrote next to the item in the source doc

### Category-specific to-do.md files

If a category already has its own to-do.md (e.g., `notes/system-design/to-do.md` exists), keep it — but the root `notes/to-do.md` is now the canonical entry point. Add an inline pointer in the root file:

```markdown
## System Design

See also: [system-design/to-do.md](system-design/to-do.md) for detailed sub-categories.
```

## Step 7: Print Summary

After all writes complete, output:

```
Action: imported <N> doc(s)
Notes created: <count>
Notes updated: <count>
Images copied: <count>
TO-DO items added: <count> (deduped: <count>)
TO-DO items skipped (already covered): <count>

Files:
- <action> <path> — <1-line summary>
- ...
```

For multi-doc imports, group the file list by source doc.

---

## Notes on input quirks

- **Google Docs URLs** with `/edit` or `/d/` typically require auth — `WebFetch` returns the login wall, not content. Tell the user to **File → Download → Markdown** and pass the file path
- **Notion exports** ship as zipped folders with images alongside markdown — point the user to unzip first, then pass the folder path
- **`.docx` with embedded images** — use `unzip file.docx -d /tmp/extracted` and look in `word/media/` for the images. The text is in `word/document.xml` — use `pandoc file.docx -o /tmp/converted.md --extract-media=/tmp/media` for the cleanest path
- **Personal abbreviations / inside jokes** the user wrote in their source — keep them only if a future reader (also the user) will still understand them in six months. If unclear, ask before paraphrasing
