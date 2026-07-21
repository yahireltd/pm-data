---
id: T-0553
title: "Quality Management: grade on a 1–10 DB scale (labels Good as new/Good/OK/Needs replaced) + up to 3 photos per grade"
type: feature
state: in_progress
created: 2026-07-14T05:07:50Z
updated: 2026-07-21T11:44:28Z
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
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - "The Quality section's 3 fixed reference-image slots (Good / Average / Bad) are replaced by 4 quality cards, one per label, each header showing the label + its score range: Good as new (8–10), Good (6–7), OK (4–5), Needs replaced (1–3)."
  - The existing 1–10 scoring stays as-is (summary tiles, check cards, Add Check modal all unchanged); the 4 labels map onto fixed ranges of the 1–10 scale (1–3 / 4–5 / 6–7 / 8–10), surfaced in the card headers.
  - Each quality card supports up to 4 reference photos shown in a 2×2 grid, with add + remove per photo (currently a single photo per slot).
  - Existing reference images are migrated onto the new cards (Bad → Needs replaced, Average → OK, Good → Good), no data loss.
  - Layout keeps the same row structure — the Failure Points / Other Notes column plus the 4 quality cards (5 equal-width columns); Other Notes stays unchanged (one-to-many notes split to T-0633).
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
relates:
  - T-0633
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 5
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
