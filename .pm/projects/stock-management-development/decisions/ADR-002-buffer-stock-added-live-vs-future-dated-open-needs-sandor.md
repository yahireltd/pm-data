---
id: ADR-002
slug: buffer-stock-added-live-vs-future-dated-open-needs-sandor
title: "Buffer stock: added live vs future-dated (OPEN — needs Sandor)"
state: proposed
decided: 2026-07-14
decided_by: []
project: stock-management-development
supersedes: null
superseded_by: null
tickets:
  - T-0559
  - T-0560
  - T-0564
version: 1
---

## Context
Buffer stock needs a way to be added and drawn down. Two models are on the table:
- **Future-dated** — buffer is entered via the Upcoming Stock page (like an upcoming delivery) and moved into stock/buffer on arrival.
- **Live** — buffer is added/adjusted immediately (Zsolt's lean: "if we have buffer and need it, we should do it live rather than future-date it").

Ben's note: effective date is irrelevant for buffer adds — we add from the upcoming page only to keep set stock levels.

## Decision
**OPEN.** To be agreed with Sandor when he's back. This choice changes the data flow for the whole buffer stream.

## Consequences
- Gates T-0559 (buffer on upcoming), T-0560 (buffer management page), and T-0564 (set stock levels + alerts).
- Determines the buffer table shape and where buffer is entered/edited.