---
id: T-0204
title: Add to sprint (existing or quick-create) from backlog
type: feature
state: done
priority: p2
created: 2026-06-04T01:35:02Z
updated: 2026-06-04T02:00:23Z
project: null
section: null
parent: null
children: []
order: 11264
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] add to sprint from backlog"
  - "[x] pick existing or add new sprint"
  - "[x] relax validation so easy sprnt creation"
  - "[x] as relaxed the sprint needs to be filled out to activate (if more detail needed )"
  - "[x] as relaxed the sprint needs to be filled out to activate (if more detail needed )"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0136
    model: claude
    started: 2026-06-04T01:36:16Z
    status: completed
    summary: Add a backlog ticket to a sprint (existing or quick-create with just a title) from the Backlog. The 'Add to sprint' button now always shows (was hidden when a project had zero sprints). Creation-relaxation + start-guard already existed and were left intact.
    ended: 2026-06-04T01:52:41Z
    test_plan: On the Backlog, a ticket WITH acceptance criteria -> 'Add to sprint' -> pick an existing sprint OR create a new one by title -> ticket commits. A ticket WITHOUT AC has the button disabled. A freshly-created planned sprint still cannot be Started until it carries committed work.
labels: []
attention: null
---

we want to be able to add to sprint from backlog\
existing or new sprint\
when creating basic inputs only can fill in the data later (to make quick and easy - perhaps make the sprint not active till filled)

## Conversation

**2026-06-04 01:52 claude:** Run run-20260604-0136 completed — Add a backlog ticket to a sprint (existing or quick-create with just a title) from the Backlog. The 'Add to sprint' button now always shows (was hidden when a project had zero sprints). Creation-relaxation + start-guard already existed and were left intact.

---

**2026-06-04 02:00 — you**

works as described
