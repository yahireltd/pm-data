---
id: ADR-003
slug: one-unified-stock-decision-board-vs-a-separate-quality-board
title: One unified stock-decision board vs a separate quality board (OPEN)
state: proposed
decided: 2026-07-14
decided_by: []
project: stock-management-development
supersedes: null
superseded_by: null
tickets:
  - T-0558
  - T-0563
version: 1
---

## Context
Two board ideas surfaced in the meeting:
- A **product-decision board** for ordering/purchasing (ordered → in transit → received…), from the Orders spike.
- A **quality decision board** for quality-driven actions (replace / buy-more), with statuses Not actioned → decision made → replacement ordered → items removed → items arrived → complete.

Ben hinted at "one magical board that governs all stock decisions" — quality, shortages, projected shortages, high-utilisation — where you action or decide-no-action.

## Decision
**OPEN.** Decide whether this is ONE unified board (quality + ordering + purchasing + shortages) or two separate boards. Resolve in the Orders spike (T-0558).

## Consequences
- If unified, T-0563 (quality board) folds into T-0558's board.
- Affects the Order Management phase (PH) and the Quality Analytics phase (PH) scope.