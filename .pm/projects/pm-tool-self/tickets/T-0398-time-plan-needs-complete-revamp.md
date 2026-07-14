---
id: T-0398
title: Time plan revamp — hide current view, rebuild at sprint level (multi-project planned-sprint roadmap)
type: feature
state: triaged
priority: p2
created: 2026-06-17T11:21:34Z
updated: 2026-07-14T12:36:11Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 112640
reporter: null
assignee: null
acceptance_criteria:
  - "[x] Current time-plan view is hidden behind a toggle/flag (not deleted)"
  - "[x] A design note (or spike) exists for the sprint-level, multi-project planned-sprint layout"
  - "[x] Build acceptance criteria are added once the design is agreed"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention:
  needed_by: human
  reason: "Backlog audit: half of this shipped under this ticket's own id — the multi-project sprint-level roadmap exists (Roadmap in the nav + per-project tab, commits de2c946/b5275c2). The other half (\"hide the current Time plan view\") was never done: Time plan is still in the project nav. Decide: close this and raise a small \"retire the old Time plan view\" ticket, or send back to finish the hiding under this one."
  since: 2026-07-14T12:23:38Z
version: 7
---

## Problem

The current time-plan view isn't useful at the level we actually plan. Keep the time-plan **concept** but **hide the current view for now**, and rebuild it at the **sprint level**: a cross-project layout that lays out multiple projects' *planned sprints* together — a portfolio roadmap of planned sprints, so you can see the whole org's sprint plan in one place.

## Context

Raised 2026-06-17, scoped 2026-06-22 (Austin). Current implementation is the CLI ASCII view at `cli/src/commands/time-plan.ts` plus a web view. Pairs with **T-0397** — the time plan is explicitly *keep-not-kill*; this ticket owns the rework.

## Design notes

- **Phase 1 (now):** hide the current time-plan entry points (reversible toggle/flag, not a delete) so it's out of the way while it's reworked.
- **Phase 2:** design a sprint-level planner — the *sprint* is the unit, laid out on a timeline, spanning multiple projects so the whole portfolio's planned sprints are visible at once.
- **Open questions to settle in design:** read-only roadmap vs editable planner? do sprints get real start/end dates or just an ordering? which projects show — all active, or a chosen set?
