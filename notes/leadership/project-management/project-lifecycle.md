# Project Lifecycle

## Key Takeaways
- Every new work request must answer three pre-PRD questions before advancing: what are we building, what value does it provide, and how do we measure success.
- A structured decision tree routes requests to one of four outcomes: add to existing production backlog, hand off to another team, wait for a shared platform to build it, or accept as a new project.
- Accepted projects follow a six-stage pipeline: idea paper → engineering decision tree → PRD → engineering scoping/design → prioritization → implementation.
- The decision tree distinguishes general platform primitives (owned by a shared platform) from product-specific functionality (owned by the product team), preventing scope creep and misaligned ownership.
- Each accepted project gets a dedicated directory with the idea paper, decision tree outcome, design docs, scoping docs, and decision logs co-located.

## Project Evaluation Workflow

Before a project is accepted onto the team's roadmap, it passes through a structured evaluation to ensure the team focuses on the highest-value work that fits its charter.

### Pre-PRD Questions

Every new work request starts with three questions (2–3 sentences each — elevator pitch, not a full doc):

1. What are we building?
2. What value does this provide for customers (external or internal)?
3. How do we measure success?

## Decision Tree

Color key: 🟩 Potential project for the team | 🟧 Maybe, evaluate ad hoc | ⬜ Not our responsibility

Decision path:

- **Is this incremental work on an existing team capability?**
  - Yes → Add to the relevant production project backlog.
  - No → Continue.

- **Should this be provided by the shared platform team?**
  - Heuristic: Is this a general platform primitive (auth, tenancy, navigation, licensing) that all products need? If yes, it belongs to the shared platform.
  - Yes → Is this functionality already provided?
    - Yes → Add to Platform Integration Tasks.
    - No / Insufficient → Is the platform team planning to build it?
      - Yes → Is there a customer who needs it sooner?
        - Yes → Consider building ad hoc (evaluate need). 🟧
        - No → Add to Platform Integration Tasks; wait for the platform team. ⬜
      - No → Will we need tight integration with the platform's services?
        - Yes → Consider building ad hoc. 🟧
        - No → Evaluate as our own project (see below).
  - No → Does this fit another product team?
    - Yes → Hand off to that team. ⬜
    - No → Does this fit within an existing production project?
      - Yes → Add to production project backlog. 🟩
      - No → New experimental project. 🟩

- **Project accepted → Write the PRD.**

## Project Adoption Flow

Once a project is accepted, it follows six sequential stages:

1. **Idea paper** — short write-up covering the pre-PRD questions (see [Idea Paper Template](./idea-paper-template.md))
2. **Engineering decision tree** — run through the evaluation workflow above
3. **PRD** — full product requirements document
4. **Engineering scoping/design** — technical design and scoping
5. **Prioritization** — stack-ranked roadmap placement
6. **Implementation**

### Project Directory Structure

Each accepted project gets a directory:

```
projects/<project-name>/
├── index.md          # idea paper content + decision tree outcome
├── design-doc.md
├── scoping-doc.md
└── decision-log.md
```

---

**Source:** Imported from internal team documentation
**Date:** 2026-06-30
**Tags:** project-management, project-lifecycle, decision-tree, prioritization, roadmap, engineering-process
