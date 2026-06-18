---
id: T-0420
title: Real "My work" — personal ticket view (assigned to me / unassigned / who's assigned)
type: feature
state: in_progress
created: 2026-06-18T08:58:41Z
updated: 2026-06-18T12:51:40Z
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
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - /me shows the current user's assigned tickets, not just projects and settings.
  - Unassigned tickets and tickets assigned to others are visible, with the assignee shown.
  - /tickets supports an assignee filter, including 'assigned to me' and 'unassigned'.
  - Admin/member users have a personal ticket view equivalent to the stakeholder 'Tickets you're following'.
  - Personal display settings remain reachable but are no longer the primary content of 'My work'.
  - The per-project List view has search and state/type filters (parity with /tickets).
  - Done/closed tickets are separated from open ones in the per-project List view.
  - The per-project List view offers a sensible sort (e.g. newest-first) alongside the manual drag order.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1251
    model: claude
    started: 2026-06-18T12:51:40Z
    status: in_progress
    summary: Claimed via web UI
labels:
  - ui
  - dogfood
  - for-austin
attention: null
version: 4
---

## Problem

There's no personal "my tickets" view for admins/members, and the per-project List view is hard to work. (For Austin.) Found while using the tool:

- **`/me` ("My work")** shows only settings (text size, avatar colour) + a list of projects — **no tickets**.
- **`/tickets`** filters by state / type / project / text, but has **no assignee filter**.
- The dashboard's **"Tickets you're following"** section only renders in the *stakeholder* view; admins/members never see it.

So a member can't answer: "what's assigned to me / what's unassigned / who is this assigned to?"

## Proposed — personal view

- Rework `/me` into an actual work view: **my assigned tickets**, **unassigned tickets**, and visibility of **who others are assigned to**.
- Add an **assignee filter** to `/tickets`, including "assigned to me" and "unassigned".
- Optionally a "My tickets" section on the admin/member dashboard (parity with the stakeholder "Tickets you're following").
- Move the personal display settings (text size, avatar colour) off the "work" surface so "My work" is about work.

## Also — per-project List view parity (finding G)

The per-project **List** view (`SectionedList`) lacks the niceties the global `/tickets` page already has, so it's hard to work:

- **No search or filter** (state/type) on the List view.
- **Done/closed tickets are mixed in**, not separated.
- **Order looks random** — it sorts by the manual `order` field (used for drag-reordering), not newest-first.

For reference, `/tickets` already has search, state/type/project filters, a collapsible closed-tickets section, and sorts by last-updated — so this is about bringing the List view closer to that, or adding a sort/browse option alongside the manual drag order.

## Note

Assignment itself is currently weak — there's no MCP way to assign a ticket to a human, and the UI assignment is limited; worth confirming the human-assignment flow as part of this.
