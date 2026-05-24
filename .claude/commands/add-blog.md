---
description: Fetch a blog post, clean it, save with images, and auto-categorize
argument-hint: <blog-url>
allowed-tools: WebFetch, WebSearch, Read, Write, Bash, Glob, Grep, Edit
---

You are adding a blog article to this notes repository. The URL is: $ARGUMENTS

Follow these steps exactly:

## Step 1: Fetch & Clean Content

Use WebFetch to retrieve the article at the given URL. In your prompt, ask for the full article content including all technical details and image URLs.

Strip the following from the extracted content:
- Advertisements and sponsored content
- Newsletter signup CTAs and subscription prompts
- Author bios and social media links
- Sidebar content unrelated to the article
- Repeated paragraphs or duplicate sections
- Navigation elements, footers, and boilerplate

Preserve all technical substance, code snippets, diagrams, and examples.

## Step 2: Extract & Download Images

Identify all meaningful diagrams, infographics, and technical illustrations from the article. For each image:

1. Download it to the `notes/images/` directory using curl
2. Name it following the convention: `yyyymmdd-hhmm-short-description.png` (use current date/time, kebab-case description)
3. Verify the download succeeded and the image is readable (use Read tool to view it)

Skip these types of images:
- Logos, avatars, and author photos
- Ad banners and promotional graphics
- Decorative separators and icons
- Images smaller than 5KB (likely icons)

## Step 3: Find Existing Content & Auto-Categorize

First, list existing topic directories inside `notes/`:

```
ls -d notes/*/
```

Then search for existing notes that cover the same or a related topic:

```
grep -ri "key terms from article" notes/ --include="*.md" -l
```

Check 3-4 key terms. There are three outcomes:

### If multiple related notes match:
- Present all matching files to the user with a brief description of each note's scope
- Use AskUserQuestion to let the user pick which file to merge into, or whether to create a new file instead
- Do NOT pick silently when there is ambiguity — always ask

### If exactly one related note exists:
- Read the existing note to understand its scope
- APPEND the new content as a new section within that file
- Add the new source URL to the metadata section at the bottom
- Update the `## Key Takeaways` section to include insights from the new content
- Update **Tags:** to include any new relevant tags
- Merge overlapping content — do not repeat information already present
- Add new images inline where they belong in the merged content

### If no related note exists:
- Determine the best topic directory using this mapping:
  - AI/ML, LLMs, prompting, RAG, agents → `notes/ai-ml-ds/concepts/`
  - Claude Code, Claude features, Anthropic tools → `notes/ai-ml-ds/claude/`
  - System design, architecture, distributed systems, databases → `notes/system-design/`
  - Leadership, management, people skills → `notes/leadership/`
  - Life coaching, personal growth → `notes/life-coaching/`
  - DevOps, CI/CD, infrastructure, cloud → `notes/devops/`
  - Backend, APIs, microservices, languages → `notes/backend/`
  - Frontend, UI, CSS, JavaScript frameworks → `notes/frontend/`
- Folders can go up to TWO levels deep inside `notes/`, then markdown files. Examples:
  - `notes/ai-concepts/llm/prompting.md` (2 folders + file)
  - `notes/system-design/caching.md` (1 folder + file)
  - NEVER deeper than `notes/<category>/<subcategory>/<file>.md`
- Use subcategories when a topic area has enough content to warrant grouping
- If the article could fit multiple directories, use AskUserQuestion to let the user choose
- If no existing directory fits, propose a new name and confirm with the user before creating
- Create a new markdown file in the chosen directory

## Special Handling: Leadership Content

If the article is about leadership, management, people skills, career growth, or organizational topics:
- Do NOT add the full blog content — leadership notes should be concise and actionable
- Write ONLY a summary with key takeaways, lessons, and actionable insights
- Use bullet points and short paragraphs — no lengthy prose from the original
- Structure as: `## Key Takeaways`, then `## Actionable Insights` (what to do differently)
- Still include source attribution and images if they are meaningful diagrams (skip stock photos)

## Step 4: Write or Update the Note

### For new notes:

Use lowercase-hyphenated filename derived from the topic (not the article title — use a broad topic name since the file may accumulate multiple sources).

Start the file with only the title as a top-level heading:

```markdown
# Topic Name
```

Then:
- `## Key Takeaways` — 3-5 bullet points summarizing the core insights
- Image references using relative paths: `![description](../images/filename.png)`
- Article content organized with clear section headers
- Keep content concise but complete — no fluff, no ads

End the file with a `---` horizontal rule followed by metadata:

```markdown
---

**Source:** <the-original-url>
**Date:** <today's date YYYY-MM-DD>
**Tags:** topic1, topic2, topic3
```

### For existing notes being updated:

- Add the new source URL to the metadata section at the bottom (use multiple **Source:** lines)
- Add new content under a clear section header (e.g., `## New Topic From Article`)
- Deduplicate — if the new article covers something already in the note, enrich the existing section rather than adding a duplicate
- Update the Key Takeaways section with any new insights
- Place new images where they contextually belong
- Update **Tags:** to include any new relevant tags

## Step 5: Print Summary

After creating or updating the note, output a brief summary:

```
Action: created | updated
Title: "<topic name>"
Category: <directory>/
File: <full-file-path>
Images: <count> saved
Sources: <count> total in file
Tags: [tag1, tag2, tag3]
```
