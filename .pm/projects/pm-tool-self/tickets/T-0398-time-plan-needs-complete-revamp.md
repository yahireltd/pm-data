---
id: T-0398
title: Time plan revamp — hide current view, rebuild at sprint level (multi-project planned-sprint roadmap)
type: feature
state: triaged
priority: p2
created: 2026-06-17T11:21:34Z
updated: 2026-06-22T14:03:39Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 112640
reporter: null
assignee: null
acceptance_criteria:
  - Current time-plan view is hidden behind a toggle/flag (not deleted)
  - A design note (or spike) exists for the sprint-level, multi-project planned-sprint layout
  - Build acceptance criteria are added once the design is agreed
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
---

## Problem

The current time-plan view isn't useful at the level we actually plan. Keep the time-plan **concept** but **hide the current view for now**, and rebuild it at the **sprint level**: a cross-project layout that lays out multiple projects' *planned sprints* together — a portfolio roadmap of planned sprints, so you can see the whole org's sprint plan in one place.

## Context

Raised 2026-06-17, scoped 2026-06-22 (Austin). Current implementation is the CLI ASCII view at `cli/src/commands/time-plan.ts` plus a web view. Pairs with **T-0397** — the time plan is explicitly *keep-not-kill*; this ticket owns the rework.

## Design notes

- **Phase 1 (now):** hide the current time-plan entry points (reversible toggle/flag, not a delete) so it's out of the way while it's reworked.
- **Phase 2:** design a sprint-level planner — the *sprint* is the unit, laid out on a timeline, spanning multiple projects so the whole portfolio's planned sprints are visible at once.
- **Open questions to settle in design:** read-only roadmap vs editable planner? do sprints get real start/end dates or just an ordering? which projects show — all active, or a chosen set?