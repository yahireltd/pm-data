---
id: T-0554
title: "Quality: multiple structured failure points per product (name + note + photo + active toggle) with affected in-stock qty"
type: feature
state: in_progress
created: 2026-07-14T05:11:11Z
updated: 2026-07-14T06:31:17Z
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
  - A product can have multiple failure points (replacing today's single free-text field on StockQualityInfo.failurePoints).
  - "Each failure point has: name, note, a photo, and an Active on/off status."
  - Active failure points can be flagged/surfaced where relevant — e.g. on the Quote Builder for sales and other views (confirm exact surfaces on pickup).
  - Each failure point shows the 'active in stock' quantity affected (how many units this exists on).
  - Existing free-text failurePoints content is preserved / migrated (not silently dropped).
  - The Quality section on the product info page lets staff add/edit/remove failure points and toggle each active.
out_of_scope: []
code_anchors:
  - path: ya-hire/common/models/StockQualityInfo.php
    note: current single free-text `failurePoints` field (L12/L32) — to be replaced by a one-to-many
  - path: ya-hire/backend/controllers/StockController.php
    note: failure-points render as textarea in buildQualitySectionContent (~L15278-15291) + save (~L20035) — rework to structured list
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: product info Quality section UI
  - path: ya-hire/common/models
    note: "NEW model + table for failure points (one-to-many per product): name, note, photo, active, + link to derive affected in-stock qty"
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
version: 3
---

## Source
Kickoff M-001 (Ben's document). Second quick-win in Milestone MS-001; same Quality screen as T-0553.

## Problem
Failure points today are a **single free-text textarea** (`StockQualityInfo.failurePoints`). You can't record them individually, attach photos, mark them active/inactive, or know how many units of stock each affects.

## What's wanted
- **Multiple** failure points per product, each with **name, note, photo, and an Active on/off** toggle.
- **Active** ones can be flagged/surfaced elsewhere (Quote Builder for sales, and other views) so people can action them.
- Show the **"active in stock" quantity** affected by each failure point — Ben: "so we know how many of these failure points exist (and can action in various ways/from various views)".

## Open questions (confirm on pickup)
1. New one-to-many table shape (per product; fields: name, note, photo, active, created_by/at).
2. Exactly which views should surface active failure points (Quote Builder + which others?).
3. How "active in stock quantity affected" is derived — per product total, or tied to specific units/batches?
4. Photo storage — reuse the same approach as the quality-grade photos in T-0553.

## Depends on / relates
- Shares the Quality section with **T-0553**; coordinate the two so the section rework is consistent.
