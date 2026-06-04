---
id: T-0185
title: Allow meeting arrangements outside of the project.
type: feature
state: done
priority: p2
created: 2026-06-03T21:35:57Z
updated: 2026-06-04T01:18:25Z
project: pm-tool-self
section: null
parent: null
children: []
order: 51200
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] We can plan meetings not related to a project"
  - "[x] we can invite attendees"
  - "[x] we can record outcomes"
  - "[x] same functionality as meetings within projects"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2209
    model: claude
    started: 2026-06-03T22:09:57Z
    status: completed
    summary: Meetings can now be planned outside a project (org-wide) with full parity — agenda, stakeholders, email + Graph calendar invites, outcomes — plus a shared org roster; fixed the M-NNN id-collision so org-meeting edits/stakeholders save.
    ended: 2026-06-04T01:00:47Z
    test_plan: Create an org meeting via the Meetings nav; add agenda/stakeholders/outcomes — all persist; emails/calendar invites fire.
labels: []
attention: null
---

We want to be able to plan meetings outside of a project also can we have a seperate area to do that with the same functionality to invite attendees with the email and calendar invites, record outcomes, add agenda etc

## Conversation

**2026-06-04 01:00 claude:** Run run-20260603-2209 completed — Meetings can now be planned outside a project (org-wide) with full parity — agenda, stakeholders, email + Graph calendar invites, outcomes — plus a shared org roster; fixed the M-NNN id-collision so org-meeting edits/stakeholders save.

---

**2026-06-04 01:18 — you**

works as expected
