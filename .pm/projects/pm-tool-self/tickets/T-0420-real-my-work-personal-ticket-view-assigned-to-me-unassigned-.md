---
id: T-0420
title: Real "My work" — personal ticket view (assigned to me / unassigned / who's assigned)
type: feature
state: review
created: 2026-06-18T08:58:41Z
updated: 2026-06-18T16:15:49Z
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
  - "[x] /me shows the current user's assigned tickets, not just projects and settings."
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
    status: completed
    summary: "Made \"My work\" actually about your work, and brought the per-project ticket list up to scratch. The \"My work\" page (/me) used to show only your projects and a couple of display settings; it now leads with the tickets assigned to you (open, newest first, with their project and status), and the text-size / avatar-colour settings move to a \"Display settings\" area at the bottom — still there, just no longer the headline. The All-tickets page gains an \"Assignee\" filter so you can show just what's assigned to you, or what's unassigned and up for grabs. And the per-project List view now has the same conveniences as the All-tickets page: a search box, filters by status and type, a sort option (manual drag order, newest, or oldest), and finished/closed tickets tucked into their own collapsible section so they don't bury the active work. Before this, a person couldn't answer \"what's assigned to me / what's unassigned?\", and the project List was hard to work because it had no search, no filters, and mixed finished work in with live work. Benefit: everyone can see their own work at a glance, and the project list is far easier to scan and work from. (We also recorded ADR-044: the top-bar count badges stay a global overview, while this personal view is the place for \"my work\".)"
    ended: 2026-06-18T14:10:22Z
    test_plan: |-
      My work (/me):
      1. Open "My work". It should lead with an "Assigned to me" list (open tickets assigned to you, with project + status). Text-size and avatar-colour settings should now sit at the bottom under "Display settings".
      2. If nothing is assigned to you, you get a friendly empty state (admins also get a "Browse unassigned work" link).

      All tickets (/tickets) — assignee filter:
      3. In the Filters panel there's a new "Assignee" row: all / mine / unassigned.
      4. "mine" shows only tickets assigned to you; "unassigned" shows only tickets with no assignee; the counts in the header update accordingly.

      Per-project List view (Projects → a project → List):
      5. There's a control bar: a search box, a state filter, a type filter, and a sort selector.
      6. Search narrows by id/title/label; state and type filters work; sort offers manual / newest / oldest.
      7. With no filters and manual sort, you can still drag tickets to reorder and between sections exactly as before. When you apply a filter or pick a non-manual sort, dragging is paused (a "drag paused" note shows) so a filtered view can't reshuffle the real order.
      8. Finished/closed tickets appear in a collapsible "Closed (N)" section at the bottom, separate from open work.
      9. Cross-impact: this is the same component used for the section board + parking lot (T-0422) — confirm the parking lot still collapses/accepts drops, and normal section reordering still works.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - dogfood
  - for-austin
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-18T14:10:22Z
version: 6
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

## Conversation

**2026-06-18 14:10 claude:** Run run-20260618-1251 completed — Made "My work" actually about your work, and brought the per-project ticket list up to scratch. The "My work" page (/me) used to show only your projects and a couple of display settings; it now leads with the tickets assigned to you (open, newest first, with their project and status), and the text-size / avatar-colour settings move to a "Display settings" area at the bottom — still there, just no longer the headline. The All-tickets page gains an "Assignee" filter so you can show just what's assigned to you, or what's unassigned and up for grabs. And the per-project List view now has the same conveniences as the All-tickets page: a search box, filters by status and type, a sort option (manual drag order, newest, or oldest), and finished/closed tickets tucked into their own collapsible section so they don't bury the active work. Before this, a person couldn't answer "what's assigned to me / what's unassigned?", and the project List was hard to work because it had no search, no filters, and mixed finished work in with live work. Benefit: everyone can see their own work at a glance, and the project list is far easier to scan and work from. (We also recorded ADR-044: the top-bar count badges stay a global overview, while this personal view is the place for "my work".)
