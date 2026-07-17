---
id: T-0607
title: £666 on cancelled split contract — cancelled value vs commission paid on team targets
type: bug
state: triaged
created: 2026-07-17T06:34:34Z
updated: 2026-07-17T08:54:06Z
project: sales-segmentation-account-management
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Nathan
  channel: meeting M-010
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - Root cause identified for the £666 showing on Paula's Q2 despite the split contract being cancelled to £0.
  - Cancelled contracts no longer contribute their value to team target / commission thresholds (per the agreed rule).
  - A clear rule is documented for cancelled commission on targets, covering paid vs invoiced.
out_of_scope: []
code_anchors:
  - path: backend/controllers/ManagementController.php
    lines: 23492-23497
    note: actionCommissionThresholds — contracts query for the quarter. Missing ->andWhere(['unconverted' => 0]); cancelled contracts are not excluded.
  - path: backend/controllers/ManagementController.php
    lines: 23506-23528
    note: Goods-value SUM from YaContractItemsIncSale. where(['i.contractID' => ...]) only — no ->andWhere(['i.unconverted' => 0]); cancelled items still summed.
  - path: backend/controllers/ManagementController.php
    lines: 23592-23675
    note: commission_splits applied here (default 50%); Paula's split share of the cancelled contract = the £666.
  - path: backend/controllers/ManagementController.php
    lines: 23767-23821
    note: actionCommissionThresholdsContracts (drill-down) — same missing unconverted filter; must be fixed here too so aggregate and drill match.
  - path: common/models/YaContracts.php
    lines: 5495-5534
    note: convertUnconvertContract() — cancel sets unconverted=1 on contract + items but never zeroes priceFixed/quotedPriceUnit/qty, and never clears the commission_splits row.
  - path: common/models/CommissionSplits.php
    note: Split rows (splitPercent, assignedUserID, splitWithUserID); only cleared by actionCommissionSplitToggle, never on cancel.
relates:
  - T-0470
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 7
milestone: MS-001
defect_status: confirmed
---

## Problem
A split contract that was **cancelled (value £0)** is still showing **£666** on Paula's Q2 results. Commission thresholds appear to be counting value that should have been removed on cancellation.

## Context
Raised by Nathan at **M-010** (Sales Phase 1 prep). Broader concern flagged in the same meeting: **cancelled commission on team targets** needs review and clear rules/logic, including **paid and invoiced** values. Owner for investigation: **Zsolt**.

## Investigation (Zsolt)
- Look into cancelled value vs commission paid: why a £0-cancelled split contract still contributes £666 to the Q2 target.
- Determine the correct rule for cancelled commission on targets (paid vs invoiced).

## Conversation

**2026-07-17 08:54 claude-code:** **Investigation — root cause confirmed (code traced, not yet fixed).**

**What's happening:** In this system a contract is cancelled by setting a flag (`unconverted = 1`) — its value is *not* zeroed. The quarterly commission/targets calculation adds up goods value from the contract's line items and never checks that flag, so a cancelled contract keeps counting. Because it's a split contract, Paula still gets her split share of it — that's the £666 on her Q2.

**The three gaps (all verified in code):**
1. The contracts query for the quarter doesn't exclude cancelled contracts — `ManagementController.php:23492` (no `unconverted = 0` filter).
2. The goods-value total is summed from the line items with no cancelled filter either — `ManagementController.php:23506-23528`.
3. Cancelling a contract (`YaContracts.php:5495` `convertUnconvertContract`) only flips the `unconverted` flag on the contract and its items — it leaves the item prices intact and leaves the split row (`commission_splits`) in place. So the value is still there to be summed, and the split % is still applied (`ManagementController.php:23592-23675`, default 50%).

Net: cancelled split contract → items still summed → × 50% split → £666 on Paula's Q2. The "£0" seen elsewhere is the contract's own display value; the targets calc ignores that and reads the untouched item rows.

**Fix direction (for when we come back):**
- Add a cancelled filter to the contracts query (`:23492`) and the drill-down (`actionCommissionThresholdsContracts`, `:23767-23821`) so aggregate and drill-down agree — mirrors the app's existing `(unconverted IS NULL OR unconverted = 0)` convention (e.g. `SalesController.php:37643`). Filtering at the contract level makes the leftover split row harmless.
- Optional hardening: clear the `commission_splits` row in `convertUnconvertContract` on cancel.

**Still a business decision (AC #3):** what cancelled value *should* count toward targets, and the paid-vs-invoiced rule — that's a Nathan/Austin call, separate from this code fix.

Code anchors added to the ticket. Not touched: the code (project is commit-restricted; ticket assigned to Zsolt).
