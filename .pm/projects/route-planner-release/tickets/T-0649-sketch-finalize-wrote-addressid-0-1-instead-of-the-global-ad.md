---
id: T-0649
title: Sketch finalize wrote addressID 0/1 instead of the global address row id
type: bug
state: done
created: 2026-07-22T19:40:44Z
updated: 2026-07-22T19:42:28Z
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
  - "[x] New sketch-finalized rows carry the global ya_contract_addr id matching the contract's delivery/collection address by type"
  - "[x] No active non-self-serve D/C rows dated 21 Jul+ remain at addressID <= 1 on live or test"
  - "[x] Self-serve rows follow the same convention (address row containing depot postcode)"
  - "[x] Copy-service-created rows (copy-to-run-planner path) write the same"
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: "finalize rc creation: addressID from deliveryAddress/collectionAddress row id"
  - path: common/services/CopyPlanToRunService.php
    note: same fix in the copy path
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1940
    model: claude-fable-5
    started: 2026-07-22T19:40:59Z
    status: completed
    ended: 2026-07-22T19:41:22Z
    summary: |-
      Every job row the new sketch planner wrote to the run system carried a wrong address reference — a 0/1 flag instead of the real link to the customer's address record that every historically-created row carries. Austin caught it by comparing this week's rows against older redelivery rows (after an earlier analysis wrongly declared the value normal). Day-to-day operations weren't visibly affected — picking and loading progressed fine, because most screens look up addresses through the booking — but anything reading the address link directly off the job row (multi-address customers, row-level address displays) would have resolved wrongly for every sketch-planned job since go-live.

      Both writing paths are fixed and deployed to live, and the existing bad rows were repaired: 40 on the test system, and the 39 live rows (all from the first real sketch finalise of next Monday's plan) via a dry-run-first script that Austin reviewed and applied himself — verified down to zero remaining and spot-checked against expected values. New finalises now write the correct address link from the start.
    test_plan: |-
      1. Finalise any date via the sketch (test box or live) → pick a created row in ya_run_contracts → its addressID must be a 9xxxx value equal to the contract's delivery (D) or collection (C) address row id — NOT 0 or 1.
      2. Verify zero remaining: SELECT COUNT(*) FROM ya_run_contracts rc JOIN ya_contracts c ON c.id=rc.contractID WHERE rc.active=1 AND rc.addressID<=1 AND rc.type IN ('D','C') AND rc.runDate>='2026-07-21' AND NOT ((rc.type='D' AND c.delMethod=5) OR (rc.type='C' AND c.colMethod=11)) → 0 on both boxes.
      3. Spot-check a repaired live row: rc 115635 should read addressID 97192.
      4. Self-serve convention: a self-collect/self-return row still resolves to its contract's address row (which contains the depot postcode) — same as the 150-row legacy sample.
      5. Cross-impact: copy-to-run-planner path (CopyPlanToRunService) shares the fix — if that flow is used, repeat check 1 on a row it creates. Address display on run planner / driver app for a multi-address customer is the highest-value visual check.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - sketch-planner
  - finalize
  - data-integrity
attention: null
version: 10
---

## Problem

Every run-contract row written by the sketch finalize (and the shared copy service) carried addressID 0 or 1 — the contract's delAddressID/colAddressID flag — instead of the global ya_contract_addr row id (9xxxx) that every legacy-written row carries. Austin caught it by comparing this week's rows against historical redelivery rows; an earlier analysis wrongly concluded 0/1 was a per-contract slot convention. Self-serve rows also carry the global id by convention (150/150 legacy sample), with the address row containing the depot postcode.

Impact: bounded — picking/loading pipelines progressed normally against 0/1 rows, so day-to-day flows resolve addresses via the contract; exposure is any consumer reading rc.addressID directly (multi-address contracts, row-level address display).

## Fix + repair (both done 22 Jul evening)

- Writers fixed (2f4c7241): finalize + CopyPlanToRunService now write deliveryAddress->id / collectionAddress->id by type — verified against real rows (contract 48394 → 96313).
- Data repaired: test box 40 rows; LIVE 39 rows (all from the 22 Jul 06:58 finalize of the 27 Jul plan) — dry-run-first script (/tmp/addressid_repair.php on the live box, re-runnable, self-serve-guarded), applied by Austin, verified zero rows remaining and spot-checked (115635 → 97192).

## Conversation

**2026-07-22 19:41 claude-code:** Run run-20260722-1940 completed — Every job row the new sketch planner wrote to the run system carried a wrong address reference — a 0/1 flag instead of the real link to the customer's address record that every historically-created row carries. Austin caught it by comparing this week's rows against older redelivery rows (after an earlier analysis wrongly declared the value normal). Day-to-day operations weren't visibly affected — picking and loading progressed fine, because most screens look up addresses through the booking — but anything reading the address link directly off the job row (multi-address customers, row-level address displays) would have resolved wrongly for every sketch-planned job since go-live.

Both writing paths are fixed and deployed to live, and the existing bad rows were repaired: 40 on the test system, and the 39 live rows (all from the first real sketch finalise of next Monday's plan) via a dry-run-first script that Austin reviewed and applied himself — verified down to zero remaining and spot-checked against expected values. New finalises now write the correct address link from the start.

---

**2026-07-22 19:42 — you**

Done
