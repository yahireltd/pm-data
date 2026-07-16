---
id: T-0561
title: "Quality: all-stock quality overview page (every product + quality level, last-check date, stats/charts, add check from there)"
type: feature
state: in_progress
created: 2026-07-14T06:36:03Z
updated: 2026-07-16T06:58:12Z
project: stock-management-development
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: kickoff M-001
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - A single page lists all products / stock lines with their current quality level/grade (the label + underlying 1–10 value from T-0553).
  - Each row shows the last quality-check date for that product.
  - Summary stats / charts of quality across the catalogue (e.g. distribution of grades, count needing replacement).
  - Staff can add a quality check directly from this page, without drilling into each product.
  - The list can be filtered/sorted (e.g. by grade, by last-check date) to surface the worst-quality lines.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: new action for the all-stock quality overview (aggregate query across products) + inline add-check
  - path: ya-hire/backend/views/stock
    note: NEW quality-overview view (table + stats/charts + add-check control)
  - path: ya-hire/common/models/StockQualityInfo.php
    note: current quality level per product (aggregate source)
  - path: ya-hire/common/models/StockQualityCheck.php
    note: dated quality checks — last-check date + add-new-check
  - path: ya-hire/common/models/YaProducts.php
    note: product list to join quality onto
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 4
milestone: MS-003
---

## Source
Kickoff M-001 (Ben's document): "Quality levels across the stock — a page with all stock and quality levels so we have an overview… stats, charts etc, add quality checks from there. Last quality check date too."

## What it is
Today quality is only visible **per product** (open a product → its Quality section). There's no way to see quality **across all stock at once**. This page is that bird's-eye view: every product listed with its current grade, so you can instantly see which lines are "Needs replaced" vs "Good as new", spot stale checks, and act.

## In scope
- All-stock table (product + current quality level + last-check date).
- Summary stats/charts (grade distribution, counts).
- Add a quality check inline from the page.
- Filter/sort to surface the worst lines.

## Out of scope (separate tickets)
- **Quality-over-time / compare-periods** view (date-range picker, score gained/lost between dates) — its own ticket; needs concepts.
- **Quality decision board** (replace / buy-more action board) — overlaps the Orders/decision-board spike (T-0558).

## Relates
- **T-0553** (grade scale + labels) and **T-0554** (failure points) — this page surfaces their data across all stock.

## Note
Core overview is fairly well-defined ("should be easy enough") — could be pulled into Sprint 1 if you want it now; the charty/analytics parts are the softer bits.
