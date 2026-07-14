---
id: T-0563
title: "Quality decision board: replace / buy-more action points on a status board (overlaps Orders decision-board spike T-0558)"
type: feature
state: triaged
created: 2026-07-14T06:48:41Z
updated: 2026-07-14T06:49:07Z
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
  - A board where quality-driven decisions live as action points per product/line (e.g. 'replace', 'buy more').
  - "Status columns capturing the lifecycle: Not actioned → decision made → replacement ordered → items removed → items arrived → complete."
  - Quality issues (from the overview T-0561 and failure points T-0554) can feed items onto the board.
  - "Design decision recorded: whether this is ONE board shared with ordering/purchasing decisions (T-0558) or a separate quality board."
out_of_scope: []
code_anchors:
  - path: ya-hire/common/models
    note: NEW decisions/board table + model (item, product, type, status, timestamps)
  - path: ya-hire/backend/controllers/StockController.php
    note: new action(s) for the board + status transitions
  - path: ya-hire/backend/views/stock
    note: NEW board view (columns/statuses)
relates:
  - T-0558
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - order-management
  - stock
  - concept
attention: null
version: 2
---

## Source
Kickoff M-001 (Ben's document): "a decision page where action points like 'replace' and 'buy more' exist on a board, e.g. Not actioned / decision made / replacement ordered / items removed / items arrived / complete… could be that ordering + purchasing decisions operate on the same page."

## Important overlap
This is essentially the **product-decision board** already described in the **Orders spike (T-0558)** — Ben's "magical board that governs all stock decisions (quality, shortage, projected shortage, high-utilisation)." 

**Key design question:** is this ONE unified decision board (quality + ordering + purchasing + shortages all on it), or a separate quality-only board? Resolve this in the T-0558 spike — they may merge into a single ticket.

## Not now
Concept-stage; parked in backlog. Should be scoped together with T-0558 so we don't build two competing boards.