# Scrum Norms

## Key Takeaways
- Two-week sprints with a rotating Scrum Master role; all ceremonies share one daily time slot (30 min standard, 60 min for planning and retros).
- Daily standups follow a four-question format per person with a parking lot for technical discussions; concurrent ticket updates are expected during standup.
- Task pointing uses a 0–8 scale; small tasks (0–1 pts) are self-pointed, larger tasks use team pointing poker; bugs are unpointed until RCA completes.
- Five-tier task priority ordering: incidents → teammate blockers → sprint spillover → customer-facing work → internal dependencies.
- Ten anti-patterns document failure modes with concrete corrective alternatives for each.

## Scrum Basics

The team follows the Scrum model.

- Each sprint is **two weeks long** (10 working days)
- Sprints start on **Wednesday** and run through the following two weeks
- At the beginning of each sprint, the team holds a sprint planning meeting to align on upcoming work

### Scrum Master Role

Each sprint has a designated Scrum Master who:
- Conducts all ceremonies
- Shares their screen during ceremonies
- Opens and closes the sprint

The Scrum Master role **rotates** among all team members.

## Ceremonies

A **shared 30-minute daily slot** covers all ceremonies, extended to **60 minutes** for sprint planning and retrospectives. No separate video meetings per ceremony.

### Daily Standup

- **When:** Every morning (e.g., 9:00–9:30 AM)
- **Duration:** Target under 30 minutes
- **First 5 mins:** Can be used to ensure individual tickets are up to date

**Format — each person covers:**
1. What I worked on yesterday and what I'm planning today
2. Am I blocked or looking for help?
3. Are my current sprint tasks on track, or is spillover likely?
4. Any cross-team dependency callouts?

An optional 2:30-minute per-person timer keeps pace.

After individual updates, a **parking lot** section handles technical discussions, blockers, or team updates.

> Treat this as a working meeting — team members update the task tracker concurrently during standup. This prevents the Scrum Master from being the only one updating tickets while everyone watches.

### Sprint Planning

- **When:** Every other Wednesday (when previous sprint closes and next begins)
- **Duration:** 60 minutes
- **Purpose:** Review tasks in the closing sprint, mark completed work done, and plan the next sprint
- Ensure no one is overloaded or underloaded going into the new sprint
- Best-effort planning, not set in stone — the goal is achievability within the sprint

### Mid-Sprint Planning

- **When:** Every other Wednesday, alternating with sprint planning
- **Duration:** ~30 minutes
- **Purpose:** Course corrections, priority changes, or points readjustments mid-sprint
- Ensures achievability of everyone's tasks in the current sprint

### Retrospective

- **When:** Every other Friday (ideally during the sprint week)
- **Duration:** 60 minutes
- **Format:** Shared visual board (e.g., Figma or equivalent) with three areas:
  - What went well (keep doing)
  - What needs improvement
  - What to stop doing
- All action items are captured as tasks in the project management tool

### Individual Project Design and Planning

1. Project lead develops scope and design once the idea paper is written
2. Lead schedules a design review meeting
3. Lead coordinates offline with relevant stakeholders for project planning

**Planning outputs:**
- All known tasks created with a Definition of Done
- Tasks assigned to tentative owners
- Tasks slotted into future sprints based on dependencies
- Tentative ETA established
- Lead presents the plan in a dedicated review meeting or as a 5-minute overview in an existing ceremony

## Task Prioritization

Task priority is subjective based on individual responsibilities, but this five-tier checklist provides a general ordering:

1. Any incident or production health task
2. Any task blocking fellow engineers inside or outside the team
3. Spillover from the previous sprint
4. Customer-facing project tasks
5. Internal tasks that will eventually block a customer-facing task

Always scan for tasks blocking fellow engineers first. Tasks with visibility outside the team should be prioritized accordingly.

## Task Pointing

### Point Definitions

| Points | Effort |
|---|---|
| 0 | Under ~2 hours (e.g., updating docs) |
| 1 | ~Half a day |
| 2 | ~Full day |
| 4 | ~Two days |
| 8 | ~Four days |
| 8+ | Too large — break it down |

### Pointing Rules

- **0-pointers** — self-pointed by the task owner
- **1-pointers** — self-pointed by the task owner
- **2, 4, or 8-pointers** — team pointing poker; if you've started the task and have strong conviction with a clear reason, you may self-point
- **Spikes** — skip pointing, or time-box with a defined outcome
- **Bugs** — start with no points until RCA is complete; point after RCA based on estimated resolution effort

## Scrum Anti-patterns

| # | Anti-pattern | Instead |
|---|---|---|
| 1 | Repeatedly changing sprint scope mid-sprint | Add to backlog first; if urgent, raise in standup, swap with a lower-priority task, update sprint scope transparently as a team |
| 2 | Skipping retrospectives | Shorten to 30 min, or collect feedback async on the shared board and review action items at the next ceremony |
| 3 | No Definition of Done on tasks | Add a DoD (one-line checklist of outcomes) to every task at creation; verify during self pre-planning before the sprint |
| 4 | Committing to unplanned non-urgent work without the team's input | Bring it up in standup parking lot and review urgency level |
| 5 | Siloed work on a story | Create a sprint task; share decisions and updates with reviewers in standup or offline on team channel threads |
| 6 | Stories too large to complete in a sprint | Break into subtasks of 8 pts or fewer before sprint planning; if you can't slice it, timebox a spike to define the breakdown |
| 7 | No backlog grooming or project-level planning | Follow the Individual Project Design and Planning process |
| 8 | Treating estimation as a deadline | Use estimates to surface risk early — flag in standup if a task is running over so the team can adjust scope, not absorb the slip silently |
| 9 | Not highlighting blockers | Call out blockers in standup the day they surface — don't wait |
| 10 | Taking tasks from too many projects in one sprint | Limit to 2–3 projects per sprint; context-switching slows delivery and makes spillover harder to predict |

---

**Source:** Imported from internal team documentation
**Date:** 2026-06-30
**Tags:** scrum, agile, sprint, ceremonies, standup, retrospective, planning, task-management, estimation, story-points, anti-patterns, team-norms
