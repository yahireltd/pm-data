---
id: T-0591
title: "Mobile pass phase 2: wide surfaces — tickets table, kanban, phases planner, settings tables"
type: feature
state: triaged
created: 2026-07-15T14:14:30Z
updated: 2026-07-15T14:14:30Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Tickets list is readable and tappable on a phone (stacked rows or cards, no horizontal page scroll)
  - Kanban board scrolls horizontally column-by-column on a phone without breaking the page
  - Settings and review pages render without overflow on a phone
  - Phases planner is at least read-usable on a tablet
  - Core action buttons are comfortably tappable (≈40px targets) on touch devices
out_of_scope: []
code_anchors:
  - path: web/app/tickets/page.tsx
  - path: web/app/_components/KanbanBoard.tsx
  - path: web/app/_components/PhasePlanner.tsx
  - path: web/app/settings/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ux
  - mobile
attention: null
version: 1
---

## Problem

Phase 1 (T-0590) made the shell responsive — sidebar drawer + compact top bar — which makes the read/record/comment flows usable on a phone. The genuinely desktop-shaped surfaces still break or need sideways squinting on small screens: the tickets table, the kanban board, the phases planner, and the settings/user tables. Only ~59 responsive utility classes exist across the app, so most components have never been given a small-screen treatment.

## Direction

Per-surface passes, mobile-usable rather than mobile-perfect: tickets table → stacked card rows below md; kanban → full-width columns with horizontal snap scrolling; phases planner → read-usable (planning can stay desktop-first); settings tables → stacked rows. Buttons/touch targets ≥40px on core actions.
