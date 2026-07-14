---
id: T-0564
title: Set stock levels (per product) + shortage/replenish cron alerts (buffer-aware)
type: feature
state: triaged
created: 2026-07-14T06:54:48Z
updated: 2026-07-14T06:54:55Z
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
  - A per-product set stock level (target/minimum) can be configured; levels are OPTIONAL (a product with no level set = no level-based alert). Consider building on the existing Stock QTY Suggestions page/mechanism.
  - A cron checks stock against set levels; when a product falls below its level, an alert/notification is sent (build / replenish / order).
  - If buffer stock is available for that product, the alert surfaces it so staff can transfer buffer → stock instead of ordering new.
  - The existing stock-shortages check is extended to send a SECOND email to Sandor when buffer stock is available for a shorted item.
  - A shortage still triggers an alert even when there is NO buffer (buffer info is included only if present).
  - "No noise: alerts don't re-fire endlessly for the same product while it stays under level (sensible de-dup/frequency)."
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: actionStockQtySuggestions (~L7482) + actionUpdateProductQtySuggestion (~L7641) — candidate home for 'set stock level' per product
  - path: ya-hire/common/models/YaProductSuggestions.php
    note: product-level suggestion model — extend to hold the set stock level
  - path: ya-hire/console/controllers/StockCheckController.php
    note: stock-check cron — add set-level check + buffer-aware alert emails
  - path: ya-hire/backend/controllers/ManagementController.php
    note: existing stock-shortage check — add the second (buffer-available) email to Sandor
  - path: ya-hire/common/models
    note: buffer-stock table (from T-0559/T-0560) — read buffer availability for the alerts
relates: []
blocks: []
blocked_by:
  - T-0559
  - T-0560
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - buffer-stock
  - stock
  - alerts
attention: null
version: 2
---

## Source
Kickoff M-001 (Sandor's ideas + Ben's notes, buffer-stock section).

## What's wanted
Stop the constant manual "do we have enough?" checking. Give products an **optional set stock level**, and have a **cron** alert when stock drops under it:
- Under level + **buffer available** → notify to **transfer/replenish from buffer** (build/replenish).
- Under level + **no buffer** → still alert (product is under its set level / shorted) so it can be ordered.
- Extend the **current stock-shortages check** to send a **second email to Sandor** when buffer stock exists for a shorted item.

Ben's steer: "Levels can be optional, but having a level enables the alerts. A shortage should alert even with no buffer, but pick up buffer with the alert if there is any."

## Depends on / relates
- **T-0559** (buffer on upcoming) + **T-0560** (buffer management page) — this reads buffer availability, so it needs the buffer table to exist first. Email: "come back to set stock levels once Upcoming and Buffer stock is finalised."

## Open questions (confirm on pickup)
1. Reuse the existing **Stock QTY Suggestions** page for setting levels, or a dedicated page?
2. Alert channel/frequency + who receives what (build vs order vs Sandor's buffer email).
3. De-dup rule so a persistently-low item doesn't email every cron run.

## Not now
Backlog / design-gated behind the buffer stream.