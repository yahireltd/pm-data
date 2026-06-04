---
id: T-0186
title: Email formatting
type: feature
state: done
priority: p2
created: 2026-06-03T21:47:29Z
updated: 2026-06-04T01:38:07Z
project: pm-tool-self
section: null
parent: null
children: []
order: 53248
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Emails need a nice template and contain relevant info from the ticket or the meeting"
  - "[x] the syling of the emal should be html and match the feel of the system"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2204
    model: claude-code
    started: 2026-06-03T22:04:31Z
    status: completed
    summary: Outbound emails now use an inline-styled branded HTML shell with richer ticket/meeting templates (priority, state, assignee, description; meeting when/where/agenda/outcomes).
    ended: 2026-06-04T01:00:47Z
    test_plan: Trigger a ticket-assigned or meeting-scheduled email and confirm the branded HTML + richer content render in the inbox.
labels: []
attention: null
---

***Emails sent out for the meetings should have a template with more information (drawn from the email info) the same with the ticket email they need more info. If possible can we also format them as html and make them look nice (match the ui of the system)***

## Conversation

**2026-06-04 01:00 claude-code:** Run run-20260603-2204 completed — Outbound emails now use an inline-styled branded HTML shell with richer ticket/meeting templates (priority, state, assignee, description; meeting when/where/agenda/outcomes).

---

**2026-06-04 01:38 — you**

Works fine now
