---
id: T-0420
title: Real "My work" — personal ticket view (assigned to me / unassigned / who's assigned)
type: feature
state: triaged
created: 2026-06-18T08:58:41Z
updated: 2026-06-18T08:58:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: email
  contact: zsolt@yahire.com
assignee: null
acceptance_criteria:
  - /me shows the current user's assigned tickets, not just projects and settings.
  - Unassigned tickets and tickets assigned to others are visible, with the assignee shown.
  - /tickets supports an assignee filter, including 'assigned to me' and 'unassigned'.
  - Admin/member users have a personal ticket view equivalent to the stakeholder 'Tickets you're following'.
  - Personal display settings remain reachable but are no longer the primary content of 'My work'.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ui
  - dogfood
  - for-austin
attention: null
version: 1
---

## Problem

There's no personal "my tickets" view for admins/members. (For Austin.) Found while using the tool:

- **`/me` ("My work")** shows only settings (text size, avatar colour) + a list of projects — **no tickets**.
- **`/tickets`** filters by state / type / project / text, but has **no assignee filter**.
- The dashboard's **"Tickets you're following"** section only renders in the *stakeholder* view; admins/members never see it.

So a member can't answer: "what's assigned to me / what's unassigned / who is this assigned to?"

## Proposed

- Rework `/me` into an actual work view: **my assigned tickets**, **unassigned tickets**, and visibility of **who others are assigned to**.
- Add an **assignee filter** to `/tickets`, including "assigned to me" and "unassigned".
- Optionally a "My tickets" section on the admin/member dashboard (parity with the stakeholder "Tickets you're following").
- Move the personal display settings (text size, avatar colour) off the "work" surface so "My work" is about work.

## Note

Assignment itself is currently weak — there's no MCP way to assign a ticket to a human, and the UI assignment is limited; worth confirming the human-assignment flow as part of this.