---
id: T-0289
title: sprints need to be in number order
type: feature
state: review
priority: p2
created: 2026-06-06T01:22:28Z
updated: 2026-06-08T16:37:40Z
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
  needed_by: human
  reason: Fixed the ordering — sprints now sort strictly by number (newest first), no status grouping, so "25, 27, 26" becomes "27, 26, 25" (commit bb3a459). Needs a deploy, then confirm + close. Can flip to oldest-first if you prefer.
  since: 2026-06-08T16:37:40Z
---

sprints lists 3 before 2 and after 1

## Conversation

**2026-06-08 14:15 claude:** Run run-20260608-1414 completed — The list of sprints now appears in a predictable number order instead of a jumbled one. Before, sprints could show out of sequence (for example sprint 1, then 3, then 2) because they were ordered by an internal manual field that didn't follow the sprint numbering. Now, within each status group (active, planned, completed, cancelled), they're ordered by sprint number with the newest first — so anyone scanning the sprints view can find the one they want at a glance. Without this, the sprint history stayed confusing to read, which makes planning and reviewing past sprints harder than it should be.

**2026-06-08 16:37 claude:** **Closing summary — follow-up fix (ordering)**

**What we did:** Made the sprints list order strictly by sprint number (newest first), and removed the status grouping that was getting in the way.

**Why we did it:** The first version sorted by number but *grouped by status first* (active, then planned, then completed). When sprints were in different statuses that pushed them out of numeric order — exactly the "25, then 27, then 26" you saw (25 active, 27 planned, 26 completed).

**What would have happened if we did nothing:** The sprints tab would keep showing numbers out of sequence whenever sprints sat in different statuses — the very thing this ticket set out to fix.

**The benefit:** Sprints now read in plain number order top to bottom (27, 26, 25, …), so the list is predictable however the sprints are spread across statuses. (It's newest-first — say the word if you'd prefer oldest-first and it's a one-line flip.)
