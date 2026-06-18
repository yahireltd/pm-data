---
id: T-0422
title: Parking lot / icebox for not-now tickets & ideas (separate from the active backlog)
type: feature
state: done
created: 2026-06-18T09:17:48Z
updated: 2026-06-18T19:06:37Z
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
  - "[x] There is a place for 'not-now' tickets that is clearly separate from the active backlog (parked items don't clutter the default triaged/backlog views)."
  - "[x] A ticket can be moved into and out of the parking lot easily, without losing its history."
  - "[x] Parked items are visibly distinct from 'we won't do this' (wontfix) and from genuinely next-up work."
  - "[x] The mechanism (new state vs section vs global view) is agreed with Ben/Austin and documented."
  - "[x] The boundary with pre-projects is clear: pre-project = future project being shaped; parking lot = ticket-level 'not now'."
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1249
    model: claude
    started: 2026-06-18T12:49:32Z
    status: completed
    summary: "Added a \"parking lot\" so tickets you're not ready to work on can be set aside without losing them — and without pretending you've decided not to do them. After agreeing the approach with you (recorded as a decision), we made it a dedicated \"Parking Lot\" area inside each project. On a project's List view there's now an \"Add parking lot\" button; once added, you simply drag a ticket into the Parking Lot to park it, and drag it back out when it's time. Parked tickets keep their full history. They're shown in their own collapsed area, clearly separate from active work and clearly different from \"won't do\" (won't-fix). They're also pulled out of the main All-tickets list into a separate \"Parked\" group, so they stop cluttering the view of what's actually next. Before this, \"not now\" items had nowhere to live except mixed into the backlog or wrongly marked won't-fix. Benefit: a clean, honest home for \"later\" that keeps the active backlog focused. (A parking lot is ticket-level \"not now\"; it's deliberately different from a pre-project, which is a whole future project being shaped.)"
    ended: 2026-06-18T13:52:44Z
    test_plan: |-
      1. Open a project's List view (Projects → a project → List). With no parking lot yet, you'll see an "Add parking lot" button next to "New section".
      2. Click it. A "Parking Lot" area appears, pinned at the bottom, collapsed, with a snowflake and a one-line caption ("not now — kept out of the active backlog, not won't-fix"). The "Add parking lot" button disappears (only one per project).
      3. Drag a ticket from any section into the Parking Lot (expand it first or drop on the collapsed strip). The ticket moves there and keeps its history; drag it back out to un-park.
      4. Go to the global All tickets page. Parked tickets should NOT appear in the main open list; instead there's a "Parked (N)" collapsible, and the header count reads "… open · N parked · … closed".
      5. Confirm a parked ticket is visibly distinct from a won't-fix one (won't-fix is a closed state in its own collapsible; parked is its own group and the ticket keeps its real state).
      6. Edge: clicking "Add parking lot" twice must not create two — it's idempotent. A project that already has a plain section named "parking-lot" gets promoted in place rather than erroring.
      7. Cross-impact: this reuses the section move/drag flow and the shared ticket reader. Sanity-check normal sections still drag/reorder, and that non-admin/stakeholder views are unaffected.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - workflow
  - dogfood
  - for-austin
attention: null
version: 13
collaborators:
  - kind: human
    name: austin@yahire.com
---

## Problem

`triaged` is one undifferentiated bucket. Everything captured-but-not-being-worked — genuine next-up work, half-formed ideas, "not now," blocked-indefinitely — all sits in the same pile, so you can't tell "what's actually next" from "someday/maybe." There's no first-class **parking lot / icebox**. (For Austin.)

Current options are workarounds, none a real "separate place":
- **`wontfix`** archives it, but reads as "we decided no", not "later".
- **A label** (`parked`/`later`) keeps it in the same list, just tagged.
- **A dedicated section** is closer but per-project and manual.
- **Pre-projects** are only for *ideas that will become whole projects*, and are heavyweight (intake gates) — overkill for parking a single ticket.

## Proposed

A place separate from the active backlog to hold parked / idea / blocked-indefinitely tickets — kept (not lost), out of the way, and easy to pull back when ready.

Design options to weigh (decision for Ben/Austin — it touches how the whole backlog is organised):
- A new **state** (e.g. `icebox` / `parked`) in the pipeline, kept out of the default backlog views.
- A dedicated **area/section** or a **global parking view**.
- vs today's label/`wontfix` workarounds.

## Boundary with pre-projects

A pre-project = a *future project* being shaped through intake gates. The parking lot = a *ticket-level* "not now." Keep them distinct.

## Note

This is partly a workflow decision, not just a build — confirm the mechanism with Ben/Austin before implementing.

## Conversation

**2026-06-18 13:52 claude:** Run run-20260618-1249 completed — Added a "parking lot" so tickets you're not ready to work on can be set aside without losing them — and without pretending you've decided not to do them. After agreeing the approach with you (recorded as a decision), we made it a dedicated "Parking Lot" area inside each project. On a project's List view there's now an "Add parking lot" button; once added, you simply drag a ticket into the Parking Lot to park it, and drag it back out when it's time. Parked tickets keep their full history. They're shown in their own collapsed area, clearly separate from active work and clearly different from "won't do" (won't-fix). They're also pulled out of the main All-tickets list into a separate "Parked" group, so they stop cluttering the view of what's actually next. Before this, "not now" items had nowhere to live except mixed into the backlog or wrongly marked won't-fix. Benefit: a clean, honest home for "later" that keeps the active backlog focused. (A parking lot is ticket-level "not now"; it's deliberately different from a pre-project, which is a whole future project being shaped.)

---

**2026-06-18 16:12 — you**

Working
