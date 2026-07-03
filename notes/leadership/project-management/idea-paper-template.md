# Idea Paper Template

## Key Takeaways
- An idea paper is a short pre-PRD document — six answers of 1–2 sentences each — written before a project enters the decision tree evaluation.
- Its purpose: provide just enough context to decide whether to pursue the work and how to categorize it.
- A structured decision tree outcome section records the evaluation path as a Mermaid flowchart — only questions actually asked are included.
- Conditional sections (Justification, Hypothesis) are required only when proceeding with ownership ambiguity or when the project is experimental.
- The idea paper becomes the canonical entry point for the project directory; all subsequent docs (PRD, design doc, scoping doc, decision log) link from it.

## Idea Paper Template

An idea paper is a short, pre-PRD document — typically 6 answers of 1–2 sentences each — written before any new project enters the decision tree. Its purpose: provide just enough context to evaluate whether the work should be pursued and how it should be categorized.

| Section | Prompt |
|---|---|
| **What is the idea?** | Describe the capability or service you want to build. |
| **Why should we do this?** | What problem does this solve? Why now? |
| **What effect will it have on the business?** | How does this impact customers, product teams, or platform capabilities? |
| **What are the requirements?** | What must be true for this to be considered complete? |
| **How will we know if this is a success?** | What metrics or signals will validate this? |
| **What do we need to do next?** | What's the immediate next step after this idea paper is accepted? |

## Decision Tree Outcome

After running the project through the engineering decision tree, record the result:

| Field | Value |
|---|---|
| **Value** | Platform play / internal tool / customer-facing — one sentence |
| **Overlap with existing systems** | Yes / No — why or why not |
| **Category** | Experimental project / Production project / Incremental work |

The decision path is rendered as a Mermaid `flowchart LR` diagram. Each node is a decision-tree question; edges are labeled with the answer chosen. Only questions that were actually asked are included — skipped branches are omitted. Final outcome node is visually highlighted.

```mermaid
flowchart LR
  A[Question 1?] -->|Yes| B[Question 2?]
  B -->|No| C[Outcome]
```

## Conditional Sections

### Justification *(only for "build despite ownership ambiguity" outcomes)*

Required when the team is proceeding with work that another team or external system should logically own. Address:
- Why the current solution is inadequate
- Why external timelines don't align
- What the handoff plan looks like

### Hypothesis *(only for experimental projects)*

State:
- The hypothesis being tested
- The explicit conditions that would cause this project to be sunset

> "What would cause us to sunset this?"

## Project Metadata *(filled after acceptance)*

| Field | Value |
|---|---|
| Status | Experimental (limited availability) / Production (generally available) |
| Owner | Team or individual |
| Repo | Link or "Not yet created" |
| Communication channel | Channel name |

## Engineering Docs Hub

Once accepted, the idea paper's directory becomes the canonical project entry point. Related docs link here as they are created:

- PRD
- Design Document
- Scoping Document
- Decision Log

---

**Source:** Imported from internal team documentation
**Date:** 2026-06-30
**Tags:** project-management, idea-paper, decision-tree, project-lifecycle, templates, engineering-process
