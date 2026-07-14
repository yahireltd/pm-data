---
id: T-0562
title: "Quality: view quality levels over time (date-range / period picker, compare periods)"
type: feature
state: triaged
created: 2026-07-14T06:48:32Z
updated: 2026-07-14T12:51:48Z
project: stock-management-development
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: kickoff M-001
assignee: null
acceptance_criteria:
  - A view shows quality levels over a selected time range (date-range picker and/or quarter/year picker).
  - One period can be compared against another (e.g. this quarter vs last).
  - Aggregates the dated quality checks in range — e.g. quality score gained/lost between two dates, per product or across stock.
  - Accessible from the products page and/or the all-stock quality overview (T-0561).
out_of_scope: []
code_anchors:
  - path: ya-hire/common/models/StockQualityCheck.php
    note: dated quality checks — the time-series source for over-time aggregation
  - path: ya-hire/backend/controllers/StockController.php
    note: new action(s) for the over-time query + comparison
  - path: ya-hire/backend/views/stock
    note: over-time view (pickers + charts); likely lives on the product page and/or quality overview
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
  - concept
attention: null
version: 2
milestone: MS-003
---

## Source
Kickoff M-001 (Ben's document): "Way to see quality levels on stock over time — a date range picker, or quarter/year picker, and maybe compare with another period." Ben: "in the distant past we had something that would pick up things saved between dates and aggregate." Flagged for a concept pass — *"maybe Claude can make us some concepts following a bit more of a chat."*

## What's wanted
See how quality trends over time, not just the current snapshot — pick a period, optionally compare to another, and see score gained/lost between dates.

## Not now
Needs a short **concept/design pass** (a couple of UI concepts to review) before build. Parked in backlog. Depends on the quality overview (T-0561) existing to hang it off.