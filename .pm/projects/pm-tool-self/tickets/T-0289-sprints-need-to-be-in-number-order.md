---
id: T-0289
title: sprints need to be in number order
type: feature
state: in_progress
priority: p2
created: 2026-06-06T01:22:28Z
updated: 2026-06-08T16:35:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 88064
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - order the sprints view correctly
out_of_scope: []
code_anchors:
  - path: web/app/_components/SprintsList.tsx
    symbol: SprintsList
    lines: 70-75
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1414
    model: claude-opus-4-8
    started: 2026-06-08T14:14:51Z
    status: completed
    ended: 2026-06-08T14:15:36Z
    summary: The list of sprints now appears in a predictable number order instead of a jumbled one. Before, sprints could show out of sequence (for example sprint 1, then 3, then 2) because they were ordered by an internal manual field that didn't follow the sprint numbering. Now, within each status group (active, planned, completed, cancelled), they're ordered by sprint number with the newest first — so anyone scanning the sprints view can find the one they want at a glance. Without this, the sprint history stayed confusing to read, which makes planning and reviewing past sprints harder than it should be.
    test_plan: |-
      1. Open a project with several sprints (e.g. pm-tool-self) → Sprints view.
      2. Confirm sprints are grouped by status (Active first, then Planned, Completed, Cancelled) and, within each group, run in descending sprint-number order (e.g. SPR-025, SPR-024, SPR-022, SPR-021) — no more out-of-sequence entries like 1, 3, 2.
      3. The active sprint still appears at the top regardless of its number.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: agent
  reason: The sprints tab on projects/deliver is showing sprint 25 then sprint 27 then sprint 26
  since: 2026-06-08T16:35:00Z
---

sprints lists 3 before 2 and after 1

## Conversation

**2026-06-08 14:15 claude:** Run run-20260608-1414 completed — The list of sprints now appears in a predictable number order instead of a jumbled one. Before, sprints could show out of sequence (for example sprint 1, then 3, then 2) because they were ordered by an internal manual field that didn't follow the sprint numbering. Now, within each status group (active, planned, completed, cancelled), they're ordered by sprint number with the newest first — so anyone scanning the sprints view can find the one they want at a glance. Without this, the sprint history stayed confusing to read, which makes planning and reviewing past sprints harder than it should be.
