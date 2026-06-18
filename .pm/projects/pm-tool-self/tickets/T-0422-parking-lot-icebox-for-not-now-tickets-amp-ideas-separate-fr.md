---
id: T-0422
title: Parking lot / icebox for not-now tickets & ideas (separate from the active backlog)
type: feature
state: triaged
created: 2026-06-18T09:17:48Z
updated: 2026-06-18T09:18:02Z
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
  - There is a place for 'not-now' tickets that is clearly separate from the active backlog (parked items don't clutter the default triaged/backlog views).
  - A ticket can be moved into and out of the parking lot easily, without losing its history.
  - Parked items are visibly distinct from 'we won't do this' (wontfix) and from genuinely next-up work.
  - The mechanism (new state vs section vs global view) is agreed with Ben/Austin and documented.
  - "The boundary with pre-projects is clear: pre-project = future project being shaped; parking lot = ticket-level 'not now'."
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
  - workflow
  - dogfood
  - for-austin
attention: null
version: 2
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