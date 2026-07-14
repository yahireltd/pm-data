---
id: T-0559
title: "Buffer stock on the Upcoming Stock page: split upcoming qty into Stock vs Buffer, add-to-stock+buffer on arrival, new buffer table"
type: feature
state: triaged
created: 2026-07-14T05:48:46Z
updated: 2026-07-14T05:48:46Z
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
assignee: null
acceptance_criteria:
  - Upcoming stock entries can be split between an 'Add to stock' quantity and a 'Buffer' quantity (Total = stock qty + buffer qty).
  - A 'Buffer' option is available on the upcoming stock page (alongside/instead of 'Addition to stock'); buffer items appear in Upcoming labelled 'Buffer' and do NOT show on the stock planner until moved into stock.
  - When stock arrives, the 'add to stock' modal has a buffer-qty field so incoming units can be allocated to stock and/or buffer.
  - A new buffer-stock table holds buffer quantities, surfaced on the same upcoming page.
  - Buffer stock is visible only to certain roles (Directors, managers, workshop) and is NOT visible to sales.
  - An upcoming entry's date and quantities can be edited (late/rescheduled deliveries).
  - The unused 'type' field is removed from upcoming stock.
  - Quantity displayed to sales vs internal is reworked so buffer is never counted as normal available stock for sales.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: actionUpcomingStock (~L4442), actionAddUpcomingStock (~L4508), actionAddRemoveUpcomingStockLive (~L4795) — add stock/buffer split, edit date/qty, remove type
  - path: ya-hire/backend/views/stock/upcoming-stock.php
    note: upcoming page UI — Buffer option, qty split, new buffer table, role-gated visibility, remove 'type'
  - path: ya-hire/common/models/UpcomingProducts.php
    note: upcoming model (+ UpcomingProductSupplierInfo) — carry stock vs buffer split
  - path: ya-hire/common/models
    note: NEW buffer-stock table + model (product, buffer qty, added_by/at, role visibility)
  - path: ya-hire/backend/views/stock
    note: the 'add to stock' modal — add a buffer-qty field on arrival
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - buffer-stock
  - upcoming-stock
  - stock
attention: null
version: 1
---

## Source
Kickoff M-001 (Ben's document, buffer-stock section). Part of the wider **Buffer Stock** design — captured here for the specific "buffer on the upcoming stock page" piece.

## What's wanted (from the email)
- On the **upcoming stock page**, choose **"Buffer"** as well as/instead of "Addition to stock", and **split the upcoming quantity** between Stock and Buffer.
- Buffer items **don't appear on the stock planner** (only once moved into stock); they show in Upcoming as **"Buffer"**.
- When the delivery **arrives**, the **"add to stock" modal** lets staff allocate incoming units to **stock and/or buffer** (buffer QTY field).
- **New table** for buffer stock, on the same page.
- Buffer visible only to **Directors / managers / workshop**; **not visible to sales** ("otherwise they'd treat it as normal stock").
- Effective date is **irrelevant** for buffer adds — added from here only to keep set stock levels.
- Also: **edit upcoming date & quantities** (late/rescheduled), **remove the unused "type"**, and **rework the quantity shown to sales vs us**.

## ⚠️ Open question — must clarify with Sandor before build
Should buffer be added **live** (immediately) rather than via a **future-dated** upcoming entry? Your note: *"if we have buffer stock and need it we should probably do it live rather than future date it… clarify with him when you're back."* This changes the data flow, so it's a blocker on the design.

## Relates / depends
- Part of the broader **Buffer Stock** stream (set stock levels + shortage/replenish alerts — separate tickets).
- Overlaps the upcoming/orders rework in the **consolidated Orders spike (T-0558)** — coordinate so "upcoming" becomes a clean step (→ stock or buffer).

## Note
- The **edit date/qty** and **remove 'type'** items could be split out as standalone quick wins if you want them done ahead of the full buffer build.