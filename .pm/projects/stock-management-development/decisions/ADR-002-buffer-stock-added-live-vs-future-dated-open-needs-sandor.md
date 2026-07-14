---
id: ADR-002
slug: buffer-stock-added-live-vs-future-dated-open-needs-sandor
title: "Buffer stock: entered via Upcoming Stock with a stock/buffer split (future-dated)"
state: accepted
decided: 2026-07-14
decided_by:
  - Zsolt
  - Sandor
project: stock-management-development
supersedes: null
superseded_by: null
tickets:
  - T-0559
  - T-0560
  - T-0564
version: 2
---

## Context
Buffer stock needed a defined way to be added and drawn down. Two models were on the table: future-dated (entered via Upcoming Stock) vs live (added immediately).

## Decision
**Future-dated, via the Upcoming Stock page.** When entering an upcoming delivery you set the incoming quantity **split** — how many units go to **stock** and how many to **buffer**. When the delivery **arrives**, you add them accordingly: some to **stock**, some to **buffer stock**.

## Consequences
- Confirms the design in **T-0559** (buffer option + qty split on the Upcoming page; add-to-stock modal gets a buffer qty on arrival; new buffer table).
- **T-0560** (buffer management page) and **T-0564** (set stock levels + shortage/replenish alerts) are unblocked.
- Buffer is entered from Upcoming only (effective date irrelevant — it's about keeping set stock levels); buffer stays hidden from sales.