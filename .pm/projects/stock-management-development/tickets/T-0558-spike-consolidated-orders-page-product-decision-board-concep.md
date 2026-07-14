---
id: T-0558
title: 'Spike: consolidated "Orders" page + product-decision board (concept + prototypes)'
type: spike
state: triaged
created: 2026-07-14T05:35:30Z
updated: 2026-07-14T05:35:30Z
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
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - orders
  - order-management
  - stock
  - concept
attention: null
version: 1
---

## Source
Kickoff M-001 (Ben's document) — flagged as needing "conceptual thinking and prototypes", so parked as a spike (NOT in Sprint 1).

## What to explore
A single consolidated **"Orders"** view / **product-decision board** that goes beyond the simple "view all orders" list (T-#5). Ideas from the meeting:
- All placed orders consolidated: **Ordered** on top, **In transit** below, **Received** moved to the bottom, with edit-in-place.
- Merge/rework **upcoming stock + suppliers** so "upcoming" is only a step (→ move to stock or buffer), and this page shows what's genuinely on the way.
- A **decision board** where product-related decisions live on one board: pending quality removes/replaces, new products considered/ordered → arrived → moved to stock/buffer/split, shortages, purchasing decisions. States like *Not actioned → decision made → replacement ordered → items removed → items arrived → complete*.
- Big blocker noted: supplier comms (Taran doesn't communicate) — the board should make order status self-evident.

## Deliverable
A short options write-up + 1–2 UI concepts/prototypes to review with Sandor & Ben, then break into real tickets. Ties into the **Buffer stock** work (buffer/upcoming rework depends on the same page).

## Not now
No build — this is discovery. Depends on the buffer-stock direction being agreed first.