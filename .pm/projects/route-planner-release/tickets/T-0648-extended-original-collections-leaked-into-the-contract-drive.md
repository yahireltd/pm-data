---
id: T-0648
title: Extended-original collections leaked into the contract-driven builders (extensions are collection-only)
type: bug
state: review
created: 2026-07-22T19:03:16Z
updated: 2026-07-22T19:08:07Z
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
  - "[x] A fully-extended original's collection is NOT emitted as a solver job on its old end date (skipped panel names it with reason fully_extended)"
  - "[x] The sketch due-list/delta does not offer it as a NEW unassigned job"
  - "[x] planner-audit check 1 counts it as an expected skip, not a missing due contract"
  - "[x] A partially-extended original still emits its (reduced) collection"
  - "[x] Extension child contracts remain delivery-excluded / collection-included everywhere"
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: fullyExtendedCollectionIds() + due-list exclusion in getContractIdsForDate
  - path: common/components/RoutePlannerNew.php
    note: buildJobsForDate collections skip + rebooked D-on-extension refusal
  - path: console/controllers/PlannerAuditController.php
    note: check 1 expected-skip
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1903
    model: claude-fable-5
    started: 2026-07-22T19:03:33Z
    status: completed
    ended: 2026-07-22T19:03:51Z
    summary: |-
      When a hire is extended, the customer keeps the goods — but the original booking still carries its old end date, and the automatic planner (which works from booking dates) didn't know the extension had cancelled that day's collection. It would have sent a vehicle to collect furniture the customer is keeping: a real live case existed the day we found it (a 254kg collection due that day, extended to the next day). The old manual run planner was immune because the extension process flags the collection at a lower level the manual screen respects — the new automatic planner never saw that flag.

      Now the automatic planner, the sketch board's job list, and the daily conservation audit all honour the same rule: a fully-extended collection is skipped (and named in the "skipped" panel so planners can see why), while a PARTIAL extension — where some items come back and some stay — still gets its reduced collection as before. Also blocked: a rebooked delivery against an extension booking (extensions never deliver — the customer already has everything). Verified against today's live-copied data: the affected collection is now skipped by name and absent from the solve.
    test_plan: |-
      On the routing test box:

      1. Fully extended: find/create a contract fully extended past its end date (extension flow on the contract screen) → solve the ORIGINAL's end date → its collection does NOT appear on any route; the solve-stats "Skipped Contracts" panel lists it with reason fully_extended.
      2. The sketch board's unassigned/delta must not offer it as a NEW job on that date.
      3. `php yii planner-audit/date <date>` → the contract shows as an expected skip (warn), NOT a FAIL.
      4. Partial extension: extend only SOME items → the original's collection still appears with the reduced weight and plans normally.
      5. Extension child: confirm it never appears as a delivery on its start date, and its collection appears on its end date (pre-existing behaviour, re-check).
      6. Cross-impact: a normal (non-extended) collection day solves and audits exactly as before; run one ordinary date end-to-end.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - solver
  - extensions
  - data-integrity
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-22T19:03:51Z
version: 9
---

## Problem

Extension contracts themselves were handled (delivery emitters exclude extendedFromContractID; collections include them). But when a hire is FULLY extended, the ORIGINAL contract keeps its old hireEndDate and the extension flow suppresses its collection at ROW level (ya_run_contracts fullyExtended=1, weight zeroed) — semantics the legacy run planner honours but the contract-driven solver job builder and sketch due-list never saw. Both would emit a collection for goods the customer is keeping (live case found 22 Jul: C093234, 254kg/43m³, ends 22 Jul, fully extended to the 23rd by C093279 — verified emitted before the fix, named-skipped after).

## Fix (3f3f2ef0, deployed to routing test box)

Shared SketchPlanService::fullyExtendedCollectionIds(date) — active C rows with fullyExtended=1 — applied to: the solver job builder (skippedJobs reason 'fully_extended', shown in the skipped panel), the sketch due-list builder (silent exclude, same as depot), and planner-audit check 1 (expected-skip, so audits stay clean). Partial extensions keep their unflagged reduced row and still collect. Also refuses D-type rebooked rows on extension contracts (extensions never deliver).

## Conversation

**2026-07-22 19:03 claude-code:** Run run-20260722-1903 completed — When a hire is extended, the customer keeps the goods — but the original booking still carries its old end date, and the automatic planner (which works from booking dates) didn't know the extension had cancelled that day's collection. It would have sent a vehicle to collect furniture the customer is keeping: a real live case existed the day we found it (a 254kg collection due that day, extended to the next day). The old manual run planner was immune because the extension process flags the collection at a lower level the manual screen respects — the new automatic planner never saw that flag.

Now the automatic planner, the sketch board's job list, and the daily conservation audit all honour the same rule: a fully-extended collection is skipped (and named in the "skipped" panel so planners can see why), while a PARTIAL extension — where some items come back and some stay — still gets its reduced collection as before. Also blocked: a rebooked delivery against an extension booking (extensions never deliver — the customer already has everything). Verified against today's live-copied data: the affected collection is now skipped by name and absent from the solve.
