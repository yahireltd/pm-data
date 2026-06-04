---
id: T-0202
title: Roster-backed autocomplete in stakeholder inputs
type: feature
state: done
created: 2026-06-04T01:00:50Z
updated: 2026-06-04T01:12:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Typing a name/email in the Add-stakeholder dialog suggests matching roster people"
  - "[x] Works across project, ticket, and meeting scopes"
  - "[x] Selecting fills name+channel+contact; free entry still allowed; people already on the entity are not suggested"
out_of_scope: []
code_anchors:
  - path: web/app/_actions/roster.ts
  - path: web/app/_components/AddStakeholderDialog.tsx
  - path: web/app/_components/StakeholderList.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0100
    started: 2026-06-04T01:00:50Z
    status: completed
    ended: 2026-06-04T01:00:50Z
    summary: "Roster-backed autocomplete in the Add-stakeholder dialog (every scope): typeahead on name/email sourced from .pm/roster.md; selecting fills name+channel+contact; dedupes people already on the entity; free entry preserved."
    test_plan: Add a stakeholder on a project/ticket/meeting -> typing suggests roster people; selecting fills the fields; manual entry still works.
labels:
  - roster
  - dx
attention: null
---

## Problem
The org roster (.pm/roster.md) auto-accumulates everyone added as a stakeholder/attendee, but only feeds the org-meeting 'from register' quick-pick. Entering a stakeholder elsewhere means re-typing.
## Ask (Austin: 'show up when entering info later in user inputs')
Roster-backed typeahead on the Add-stakeholder dialog across project/ticket/meeting — suggestions surface as you type, sourced from the roster.

## Conversation

**2026-06-04 01:00 claude-code:** Run run-20260604-0100 completed — Roster-backed autocomplete in the Add-stakeholder dialog (every scope): typeahead on name/email sourced from .pm/roster.md; selecting fills name+channel+contact; dedupes people already on the entity; free entry preserved.

---

**2026-06-04 01:12 — you**

works
