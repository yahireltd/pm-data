---
id: T-0631
title: 'Spike: a "stock received / restocked" event for hire stock'
type: spike
state: triaged
created: 2026-07-21T08:24:23Z
updated: 2026-07-21T08:24:23Z
project: stock-management-development
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: follow-up from T-0554
assignee: null
acceptance_criteria:
  - Determine whether a discrete 'stock received / restocked' event should reuse StockTransactions/StockLevels or be a new lightweight action, and where it fits.
  - Define the minimal event shape (qty, date, note, user) and how it writes a stock movement.
  - Identify the consumers (T-0554 batch review-nudge, intake audit, level reconciliation) and what each needs from the event.
  - Produce an effort estimate + build-or-not recommendation to discuss with Ben (and maybe Sandor).
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSaveStockTransaction (~L2173) — existing manual stock movement entry; actionUpdateStockQtyFromInsphire (~L2459) is the dormant InspHire sync
  - path: ya-hire/common/models
    note: StockLevels (running total_stock) + StockTransactions (movements) — where a 'received' event would write
  - path: ya-hire/backend/views/stock/view-all-products.php
    note: "'Update Stock Qty From Insphire' button (L176) — the dormant path, not the live process"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - stock
  - quality-management
attention: null
version: 1
---

## Problem
The app has **no discrete "stock received / restocked" moment** for hire stock. A product's quantity is just a running number; there's no event that says "we took N of this back in / replenished it". (A dormant InspHire-shaped `Stock`/`StkTrans` integration exists in the code — the "Update Stock Qty From Insphire" button on `view-all-products` — but **InspHire is not used**; it is not the live process and shouldn't be relied on.)

## Why it matters
Surfaced while building **T-0554** (structured quality failure points). We wanted the batch-scoped failure-point review-nudge to fire *"when this product is restocked"* — but there's no event to hook, so staleness fell back to a **30-day timer**. A real stock-received event would let the nudge fire at the right moment, and likely helps elsewhere too (intake auditing, level reconciliation, accuracy).

## What to explore (spike)
- Where a lightweight **"stock in / received"** action fits alongside the existing `StockLevels` / `StockTransactions` tables and `actionSaveStockTransaction` — reuse vs new.
- Minimal event shape: qty, date, note, who — writing a stock movement.
- Consumers: (a) T-0554 batch review-nudge, (b) intake audit trail, (c) level reconciliation.
- Effort estimate + a build-or-not recommendation to discuss with **Ben (and maybe Sandor)**.

## Relates
- Follow-up from **T-0554** (structured failure points). See its "Noted gap" comment (2026-07-21).

## Notes
Raised as a discussion/scoping item — for the conversation with Ben/Sandor, not yet committed to build.