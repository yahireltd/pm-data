---
id: T-0643
title: Contract stuck at 'Partially Delivered' (49) after a failed delivery is successfully redelivered
type: bug
state: triaged
created: 2026-07-22T15:54:04Z
updated: 2026-07-22T15:58:03Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Completing a redelivery advances the contract to 50 Delivered / Awaiting Col (not stuck at 49)
  - The redelivery row itself reaches 50 via the normal driver-app completion path
  - A genuinely partial state (some items still undelivered, no completed rebooking) still reads 49
  - Failed-jobs page stops flagging the original once its redelivery chain is complete
out_of_scope: []
code_anchors:
  - path: backend/controllers/LogisticsController.php
    note: re-delivery endpoint (~2942) and failed-jobs flows; status aggregation lives in the delivery-completion path used by the driver app
  - path: common/models/YaRunContracts.php
    note: rebookedRunContractID chain; resetClonedOperationalState added 5c845d42
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - logistics
  - status-flow
  - redelivery
attention: null
version: 2
---

## Problem

When a delivery fails and is rebooked, completing the redelivery does not advance the contract's status: C093283 stayed at 49 "Partially Delivered" on the contract AND on the redelivery row after the redelivery happened (22 Jul). The status aggregation appears to keep counting the failed original row as an incomplete delivery even though it carries rebookedRunContractID pointing at a completed replacement.

Austin corrected the data by hand this time; the aggregation should resolve it automatically.

## Fix shape

Wherever contract/run-contract status is recomputed from delivery rows, a failed row whose rebookedRunContractID chain ends in a completed (status >= 50) active row should count as satisfied — the contract should read 50 "Delivered / Awaiting Col" once the redelivery completes. Note the born-clean clone fix (5c845d42) already stops redelivery rows being BORN at 49 with inherited driver stamps; this ticket is the remaining aggregation/completion-path piece.

## Conversation

**2026-07-22 15:58 claude-code:** **Diagnosis correction after reading YaRunContracts::afterSave (Austin's prompt).** The status aggregation was NOT counting the failed original — getSiblings() already filters failed=1 and inactive rows. The actual chain: the redelivery row was BORN at status 49 with the failed attempt's driver stamps (the clone bug), whatever completed the redelivery never advanced the row, and afterSave then correctly propagated 49 to the contract.

Consequence: the born-clean clone fix (5c845d42) most likely resolves this ticket's future cases on its own — a redelivery now starts at 0/Planning, the driver completion takes it to 50, and afterSave lifts the contract to 50. The aggregation's "highest − 1 = Partially" branch is legitimate multi-piece behaviour, not a defect.

**Remaining to verify before closing:** why C093283's actual redelivery completion didn't move the row 49→50 on the day — either the driver never marked it in the app (completed off-app), or the completion path refuses/skips rows already at ≥49. Suggest testing one real redelivery cycle on the test box post-5c845d42: create redelivery from a failed job → confirm row born at 0 → complete via the driver flow → row 50, contract 50, failed-jobs page clear. If that passes, close as resolved-by-5c845d42.

Also noted while in there: the afterSave reads Yii::$app->controller->action — any console-context status write would fatal. Finalize only inserts rows or updates without status changes, so it's not currently reachable, but worth knowing.
