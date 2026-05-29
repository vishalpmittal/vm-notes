---
description: Add concise course notes from text, URL, or image to a course file + update glossary
argument-hint: <course-file> <text, url, or image path>
allowed-tools: WebFetch, Read, Write, Bash, Edit, Grep, Glob
---

You are adding course notes to this repository. Input: $ARGUMENTS

## Step 1: Parse Input

The arguments contain a **target file path** and **content source**. The target file is the first argument ending in `.md` or referencing a known course file. Everything after is the content source.

- If the content source starts with `http` → fetch via WebFetch
- If the content source is a file path (e.g., ends in `.png`, `.jpg`, `.pdf`) → read via Read tool
- Otherwise → treat as pasted text

## Step 2: Extract Key Points

From the content (fetched, read, or pasted), extract:

1. **Key points** — ultra-concise bullet points, no prose
2. **Key terms** — technical terms, concepts, acronyms worth defining

Rules:
- Be extremely concise — one line per bullet, no fluff
- Use bullet points only, no paragraphs
- Strip ads, CTAs, bios, boilerplate if from a URL

## Step 3: Append to Course File

Read the target course file. Append a new `##` section **before** the `---` metadata separator at the bottom.

- Pick a short, descriptive section heading based on the content
- Use concise bullets only
- If the file has no content yet (only title + metadata), add under `## Key Takeaways` if it exists, or create a new section
- Never overwrite existing content — always append
- If a source URL exists, add it to the metadata section at the bottom

## Step 4: Update Glossary

Find or create `glossary.md` in the **same directory** as the target course file.

### If glossary.md does not exist, create it:

```markdown
# Glossary

| Term | Definition | Reference |
|---|---|---|
```

### For each key term extracted:

- Check if the term already exists in the glossary
- If it exists and the new definition is richer → update the definition
- If it does not exist → add a new row
- **Definition:** one line, plain language
- **Reference:** link to the section in the course file using `[section-name](filename.md#section-anchor)` format (lowercase, hyphens for spaces in anchor)
- Keep the table sorted alphabetically by term

## Step 5: Print Summary

```
File: <path>
Added: "<section heading>"
Glossary: <N> terms added/updated in glossary.md
```
