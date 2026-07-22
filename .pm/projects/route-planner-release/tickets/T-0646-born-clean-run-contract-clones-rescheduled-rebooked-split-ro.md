---
id: T-0646
title: "Born-clean run-contract clones: rescheduled/rebooked/split rows inherited status and driver stamps"
type: bug
state: done
created: 2026-07-22T16:20:27Z
updated: 2026-07-22T16:38:05Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Moving a contract's hire date creates holding rows at status 0 with no arrival/driver stamps"
  - "[x] Creating a re-delivery from a failed job creates the new row at status 0 with no inherited stamps"
  - "[x] The split-collection copy creates the collection row clean"
  - "[x] Sale-contract date moves behave the same"
  - "[x] Driver completing a redelivery advances row and contract to 50 (verifies T-0643)"
out_of_scope: []
code_anchors:
  - path: common/models/YaRunContracts.php
    note: resetClonedOperationalState(); date-move helper call site
  - path: backend/controllers/LogisticsController.php
    note: re-delivery endpoint (~2942) + split-collection copy (~795)
  - path: common/models/YaContracts.php
    note: afterSave hireStartDate/hireEndDate inline date-move blocks
  - path: common/models/YaContractsSale.php
    note: same two blocks, sale variant
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1620
    model: claude-fable-5
    started: 2026-07-22T16:20:42Z
    status: completed
    ended: 2026-07-22T16:21:01Z
    summary: |-
      When a booking is rescheduled, a failed delivery is rebooked, or a split collection is created, the system builds the new job row by copying the old one — and it was copying too much: the old attempt's "delivered" status, driver arrival times and GPS stamps came along for the ride. That produced jobs that looked already-delivered before a vehicle ever left (one rescheduled contract read "Done" with May's timestamps on its July delivery; a redelivery was born carrying the failed attempt's arrival time and stuck the whole contract at "Partially Delivered"). The warehouse works off these statuses, so a job born "delivered" can silently skip picking.

      Every one of the seven copy paths now wipes the per-attempt history at creation: new rows start at "Planning" with no inherited stamps, whichever door they come through — reschedule, rebooking, or split. Austin corrected the two affected live contracts by hand; this stops the factory producing more. Without it, every date change made by sales kept minting poisoned rows daily.
    test_plan: |-
      On the routing test box (deployed; live gets it with the pending pull — priority, since sales date-edits mint bad rows until then):

      1. Date move: take a contract with a delivered/collected history row, change its hireStartDate in sales → the new date's holding row (runID -1) must be status 0 with NULL arrival/departure/driver times. Same for hireEndDate → collection row.
      2. Re-delivery: mark a delivery failed on the failed-jobs page, create the re-delivery → new row status 0, no stamps; original marked failed with rebookedRunContractID set.
      3. Split collection: run the split-collection flow → collection row born clean.
      4. Sale contract date move: repeat test 1 on a sale contract.
      5. T-0643 verification: complete the re-delivery from test 2 via the driver flow → row reaches 50, CONTRACT reaches 50 (not 49), failed-jobs page stops flagging it.
      6. Cross-impact: normal (non-clone) status progression untouched — deliver a normal job end-to-end and watch statuses climb the ladder; contract afterSave date-move still ghosts planned rows and emails logistics for loaded contracts as before.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: written
labels:
  - logistics
  - status-flow
  - data-integrity
attention: null
version: 11
---

## Problem

Seven code paths create a new ya_run_contracts row by cloning an existing one via setAttributes() and resetting only the planning fields — the status ladder, ETA times, driver arrival/departure stamps, GPS-at-arrival and late/short flags all rode along. Real damage found on live (22 Jul): C091260's delivery, rescheduled May→July, was born 'Delivered' on its new date with the May attempt's stamps (contract stuck at 130 Done); C093283's redelivery was born at 49 'Partially Delivered' carrying the FAILED attempt's driver arrival, and the contract-status afterSave then faithfully propagated 49 to the contract. Warehouse pipelines key off this ladder, so a born-'delivered' row can skip picking entirely.

## Fix (two commits, deployed to routing test box)

New YaRunContracts::resetClonedOperationalState() — resets status to 0/Planning and nulls all per-attempt fields — called after every clone: the failed-jobs/run-planner re-delivery endpoint, the model date-move helper, the split-collection copy, and the four inline date-move blocks in YaContracts::afterSave and YaContractsSale::afterSave (hireStartDate + hireEndDate — the paths a sales-side date edit actually takes, and the ones that built C091260's rows).

Austin hand-corrected the two live contracts. Related: T-0643 (stuck-at-49 — likely resolved by this fix, pending one verification cycle) and T-0642 (redeliveries invisible to the solver).

## Conversation

**2026-07-22 16:21 claude-code:** Run run-20260722-1620 completed — When a booking is rescheduled, a failed delivery is rebooked, or a split collection is created, the system builds the new job row by copying the old one — and it was copying too much: the old attempt's "delivered" status, driver arrival times and GPS stamps came along for the ride. That produced jobs that looked already-delivered before a vehicle ever left (one rescheduled contract read "Done" with May's timestamps on its July delivery; a redelivery was born carrying the failed attempt's arrival time and stuck the whole contract at "Partially Delivered"). The warehouse works off these statuses, so a job born "delivered" can silently skip picking.

Every one of the seven copy paths now wipes the per-attempt history at creation: new rows start at "Planning" with no inherited stamps, whichever door they come through — reschedule, rebooking, or split. Austin corrected the two affected live contracts by hand; this stops the factory producing more. Without it, every date change made by sales kept minting poisoned rows daily.

---

**2026-07-22 16:38 — you**

done
