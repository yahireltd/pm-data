---
id: T-0200
title: Meeting planning ticket linking
type: feature
state: done
priority: p2
created: 2026-06-04T00:14:43Z
updated: 2026-06-04T01:10:20Z
project: null
section: null
parent: null
children: []
order: 8192
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] we can search tickets by title / body /number when adding a ticket to agenda / anywhere else"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0015
    model: claude
    started: 2026-06-04T00:15:41Z
    status: completed
    summary: Meeting agenda ticket-link is now a searchable picker (filter open tickets by id/title) instead of a raw T-NNNN box; free text still works.
    ended: 2026-06-04T01:00:50Z
    test_plan: Add an agenda item -> the ticket field searches open tickets live; selecting fills the id.
labels: []
attention: null
---

we should list out unclosed tickets and filter by name and number when adding agenda and entering text into the ticket input - %filter% on number and title/body

## Conversation

**2026-06-04 01:00 claude:** Run run-20260604-0015 completed — Meeting agenda ticket-link is now a searchable picker (filter open tickets by id/title) instead of a raw T-NNNN box; free text still works.

---

**2026-06-04 01:10 — you**

works perfectly
