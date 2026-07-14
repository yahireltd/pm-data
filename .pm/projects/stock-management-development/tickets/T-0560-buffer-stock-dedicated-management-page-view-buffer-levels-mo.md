---
id: T-0560
title: "Buffer stock: dedicated management page (view buffer levels, move buffer ↔ live stock, role-gated)"
type: feature
state: in_progress
created: 2026-07-14T06:35:49Z
updated: 2026-07-14T06:54:54Z
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
  - A dedicated Buffer Stock management page lists buffer quantities per product.
  - Staff can move buffer into live stock (and, where appropriate, back), with each movement logged (who/when/qty).
  - The page is role-gated (Directors / managers / workshop) and NOT visible to sales.
  - It reads from the same buffer-stock table introduced in T-0559 (single source of truth for buffer qty).
  - Buffer levels here feed the shortage/replenish alerting (set stock levels ticket) rather than being a separate silo.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: new action for the buffer management page + move buffer↔stock (mirror upcoming add/remove flows ~L4508/4795)
  - path: ya-hire/backend/views/stock
    note: NEW buffer-stock management view (list + move controls, role-gated)
  - path: ya-hire/common/models
    note: buffer-stock table/model shared with T-0559
  - path: ya-hire/common/models/StockLevels.php
    note: live stock levels — target of buffer→stock moves
relates: []
blocks:
  - T-0564
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - buffer-stock
  - stock
attention: null
version: 3
---

## Source
Kickoff M-001 (buffer-stock section). The dedicated buffer page, beyond the upcoming-stock split (T-0559).

## What's wanted
A standalone place to **see and manage buffer stock**: current buffer per product, and the ability to **move buffer into live stock** when a shortage hits (the whole point of buffer). Role-gated to Directors/managers/workshop; hidden from sales.

## Depends on / relates
- **T-0559** — introduces the buffer table + the upcoming-page split; this page manages what lands there.
- **Set stock levels + shortage/replenish alerts** (separate ticket) — buffer availability is what those alerts check.
- Blocked on the same buffer design direction (live vs future-dated) being agreed with Sandor.

## Not now
Parked in backlog until the buffer design is confirmed; pull into a sprint once T-0559's direction is settled.
