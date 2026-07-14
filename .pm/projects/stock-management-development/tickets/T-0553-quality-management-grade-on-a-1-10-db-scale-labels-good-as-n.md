---
id: T-0553
title: "Quality Management: grade on a 1–10 DB scale (labels Good as new/Good/OK/Needs replaced) + up to 3 photos per grade"
type: feature
state: ready
created: 2026-07-14T05:07:50Z
updated: 2026-07-14T05:08:51Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: kickoff M-001
assignee: null
acceptance_criteria:
  - "Quality grade is captured using the labels: Good as new / Good / OK / Needs replaced."
  - The grade is stored in the DB on a reserved 1–10 numeric scale (the 4 labels map onto fixed points on the 1–10 range), so the scale can be made more granular later without losing or reinterpreting past inputs.
  - Each grade supports multiple photos — up to 3 (currently 1).
  - Existing quality data (current grade + single photo) is preserved and maps onto the new scale / multi-photo model with no data loss.
  - The Quality section on the product info page shows the label (not just the raw number) with its photos.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: buildQualitySectionContent() (~L15181) + renderSection('quality') (~L12698) — quality tab render + edit modal
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: product info page — Quality section UI (grade display + photos)
  - path: ya-hire/common/models/StockQualityInfo.php
    note: quality grade + photo storage — where the 1–10 scale and multi-photo set live
  - path: ya-hire/common/models/StockQualityCheck.php
    note: per-check quality record (dated grade entries)
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
version: 2
---

## Source
From the kickoff meeting (M-001) — Sandor's + Ben's stock documents. First quick-win in Milestone MS-001.

## Problem
Quality grading today is fixed **good / average / bad** with **one photo each**, and there's no consistent, future-proof numeric scale — so we can't get more granular later without reworking/losing past inputs.

## What's wanted
- **Simpler labels now:** Good as new / Good / OK / Needs replaced.
- **Store on a 1–10 DB scale** so the labels sit on a reserved numeric range — we can expand granularity in future without losing the completed scoring (Ben's "align labels with a 1–10 DB scale" idea; kept simple for now per the "simpler is better for now" steer).
- **Up to 3 photos per grade** (currently 1).

## Open questions (confirm on pickup)
1. Exact mapping of the 4 labels onto the 1–10 points (e.g. 10 / 7 / 4 / 1?).
2. Photo storage: extend the existing single-photo field to a set of up to 3 — confirm storage location/format matches how other stock photos are handled.
3. Does the grade live on `StockQualityInfo` (current state) or per-check on `StockQualityCheck` (history), or both?

## Out of scope (separate tickets)
- Failure points rework, the all-stock quality overview page, and quality-over-time views — those are their own tickets in this milestone.