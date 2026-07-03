# Project Execution Process

## Key Takeaways
- Accepted projects move through three sequential phases before implementation: stakeholder alignment (idea paper), parallel planning (PRD + eng design + UX designs), and project kick-off.
- Parallel planning — writing the PRD, engineering design doc, and UX designs simultaneously — reduces cycle time and surfaces technical constraints before product requirements are locked.
- The idea paper is a lightweight alignment artifact written by whoever has the idea (engineer, PM, or designer); exit criteria are stakeholder buy-in and assigned owners for downstream artifacts.
- A dedicated async channel, shared draft snapshots, and weekly syncs keep parallel planning artifacts consistent before kick-off.
- The project tracker uses seven discrete phases (Inbox → Evaluate Need → Proposed/Accepted → Planning → Committed → Implementation → Complete).

## Overview

After a project is accepted (per the [project lifecycle process](./project-lifecycle.md)), it moves through three phases before implementation begins:

```
Idea Paper → [Parallel: PRD + Eng Design Doc + Designs] → Project Kick-off → Implementation
```

## Phase 1: Idea Paper (Stakeholder Alignment)

Whoever has the idea (engineer, PM, or designer) writes the idea paper and opens a pull/merge request.

The idea paper must cover:
- **What is being built and why**
- **Scope** — does not need to be final; details are refined during parallel planning
- **How it fits into the existing product** — where it lives in the UX, which services it touches, dependencies
- **Decision tree outcome** — run through the team's project evaluation decision tree

What happens:
1. Author writes the idea paper and opens a PR/MR
2. Product, engineering, and design stakeholders review it
3. Open questions surface during review; non-blocking ones are deferred to parallel planning
4. Once aligned, the PR/MR is merged and the idea paper becomes the permanent project record

**Exit criteria:**
- Idea paper PR/MR is merged
- Stakeholders agree the project is worth planning
- Owners are assigned for the PRD, eng design doc, and designs

## Phase 2: Parallel Planning

The PRD, engineering design doc, and designs (if applicable) are written simultaneously rather than sequentially.

**Why parallel?** Prevents late-stage surprises (e.g., engineering discovering a technical constraint after the PRD is locked) and reduces cycle time since phases don't block each other.

**Roles:**
- **PRD owner** — writes product requirements, user stories, and acceptance criteria
- **Eng design doc owner** — writes the technical approach, system design, and implementation plan
- **Design owner (if applicable)** — produces wireframes, flows, or prototypes

**Example cross-artifact conflicts resolved in this phase:**
- Eng design reveals a product requirement is technically expensive → PRD owner adjusts scope before it is locked
- Designs uncover UX flows the PRD hadn't considered → PRD owner adds them
- PRD clarifies a business constraint that changes the technical approach
- Designs propose an interaction requiring a new API endpoint → engineering and design resolve it before either artifact is finalized

**Coordination mechanics:**
- Create a dedicated async project channel (e.g., `#proj-<project-name>`) for coordination during planning
- Share early drafts per the team's commenting workflow
- Run weekly syncs or post async updates to flag conflicts between artifacts
- Each artifact links to the others

**Exit criteria:**
- All applicable artifacts are in a reviewable state
- Artifacts are consistent with each other (eng design addresses PRD requirements; designs match specified flows)

## Phase 3: Project Kick-off

**What happens:**
1. Project owner schedules a kick-off with the full project team, including implementing engineers and stakeholders from dependent teams
2. Walk through the PRD, eng design doc, and designs together
3. Produce a scoping document defining workstreams, milestones, and an initial task breakdown
4. Identify dependent teams and coordinate timelines and integration points
5. Confirm the project category (experimental or production) and infrastructure requirements

**Exit criteria:**
- Scoping document is complete
- The team has a shared understanding of what they're building and how
- Project tracker project is created for task tracking
- Repo or subdirectory is set up based on the eng design doc
- Initial tasks are created and assigned

## Project Tracker Phases

| # | Phase | Description |
|---|---|---|
| 1 | Inbox | New work request, not yet evaluated |
| 2 | Evaluate Need | Running through the decision tree |
| 3 | Proposed / Accepted | Idea paper PR/MR is merged |
| 4 | Planning | PRD, eng design doc, and designs in parallel |
| 5 | Committed | Project kick-off complete, ready for implementation |
| 6 | Implementation | Active development |
| 7 | Complete | Done |

---

**Source:** Imported from internal team documentation
**Date:** 2026-06-30
**Tags:** project-management, project-execution, planning, stakeholder-alignment, kick-off, prd, engineering-design, workflow, process
