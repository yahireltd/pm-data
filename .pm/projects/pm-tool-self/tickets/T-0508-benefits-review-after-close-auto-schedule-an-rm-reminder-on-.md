---
id: T-0508
title: "Benefits review after close: auto-schedule an RM- reminder on project close"
type: feature
state: review
created: 2026-07-02T13:38:42Z
updated: 2026-07-02T13:48:19Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Closing a project creates an active RM- reminder ~90 days out titled 'Benefits review — [project name]' targeting the project (dashboard links to /projects/[slug])
  - Reminder creation is best-effort — failure never blocks the close
  - "No duplicate: closing twice (or re-closing) doesn't create a second active benefits-review reminder for the same project"
out_of_scope: []
code_anchors:
  - path: web/app/_actions/projects.ts
    symbol: closeProject
  - path: web/app/_actions/reminders.ts
    symbol: createReminder (reused)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1346
    started: 2026-07-02T13:46:19Z
    status: completed
    ended: 2026-07-02T13:48:19Z
    summary: "When you close a project, the tool now automatically schedules a follow-up to ask whether the project was actually worth it. Before, a project could close and nobody would ever check whether it delivered the value it was started for — the justification was written at the start and never revisited. Now, closing a project quietly creates a reminder about three months out called \"Benefits review — [project]\". It surfaces on the dashboard a week before and on the day, and links back to the project so you can compare what the charter promised with what actually happened. If a benefits review is already scheduled for that project, closing again won't create a duplicate, and if anything goes wrong creating the reminder, the close itself still succeeds. Without this, we'd keep finishing projects without ever learning which ones paid off. Benefit: every project gets a scheduled \"did it deliver?\" moment, automatically."
    test_plan: "1. Close a test project (meet the closure gates from T-0507, or force-close with a reason). 2. Open /reminders: an active reminder \"Benefits review — [project name]\" exists, anchored ~90 days from today with 7-day and on-the-day lead times, target shown as \"project [slug]\". 3. On the dashboard, confirm the reminder's target link opens /projects/[slug]. 4. Duplicate guard: reopen the project (state back from done) and close it again → still only ONE active benefits-review reminder for that project. 5. Dismiss the reminder, close/reopen again → a new one IS created (dismissed ones don't block). 6. Regression: closing still works when the reminders directory is empty/missing (reminder creation is best-effort and never blocks the close); T-0507's gate behaviour is unchanged."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - closure
  - reminders
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T13:48:19Z
version: 4
---

## Problem

Nothing ever asks "did the project deliver what its case promised?" after closure — the PRINCE2 benefits-review gap. Projects close and are never reconciled against the value they were justified on.

## Design

Closing a project auto-creates a global RM- reminder (~90 days out, offsets 7d/0d, dashboard channel) titled "Benefits review — [project]", targeting the project so the dashboard links back to it. Best-effort: a reminder failure never blocks the close; skip creation if an active benefits-review reminder already targets the project.

## Conversation

**2026-07-02 13:48 claude-code:** Run run-20260702-1346 completed — When you close a project, the tool now automatically schedules a follow-up to ask whether the project was actually worth it. Before, a project could close and nobody would ever check whether it delivered the value it was started for — the justification was written at the start and never revisited. Now, closing a project quietly creates a reminder about three months out called "Benefits review — [project]". It surfaces on the dashboard a week before and on the day, and links back to the project so you can compare what the charter promised with what actually happened. If a benefits review is already scheduled for that project, closing again won't create a duplicate, and if anything goes wrong creating the reminder, the close itself still succeeds. Without this, we'd keep finishing projects without ever learning which ones paid off. Benefit: every project gets a scheduled "did it deliver?" moment, automatically.
