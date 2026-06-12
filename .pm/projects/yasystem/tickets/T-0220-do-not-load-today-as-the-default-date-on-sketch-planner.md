---
id: T-0220
title: Do not load today as the default date on sketch planner
type: feature
state: review
priority: p2
created: 2026-06-04T14:04:15Z
updated: 2026-06-12T01:15:55Z
project: yasystem
section: null
parent: null
children: []
order: 17408
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - By default load no date
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1416
    model: claude
    started: 2026-06-04T14:16:36Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention:
  needed_by: human
  reason: Implemented & committed (8b37aef5); ready for human review/testing — test steps in the Conversation.
  since: 2026-06-04T18:03:07Z
branch: PickingSketchSalesDashFriday
version: 4
---

## Problem

_Suggested feature from meeting M-003._

Do not load today as the default date on sketch planner

## Conversation

**2026-06-04 18:02 claude-code:** **Done** — committed `8b37aef5`.

`actionSketch` no longer defaults to today; with no `date`/`id`/`solver_run` it renders a bare date-picker landing (`sketch-pick-date.php`).

**To test:**
1. Open `/route-planner/sketch` with no params → should show a **"Select a date to plan"** picker, not today's plan.
2. Pick a date → loads that date's plan. (Existing date links / the in-app date nav are unaffected.)
