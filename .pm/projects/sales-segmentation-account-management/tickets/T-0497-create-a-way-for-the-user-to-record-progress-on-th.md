---
id: T-0497
title: Account Elevation tracker — record + visualise a customer's climb to their proposed bucket ('in the bag %')
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:32:38Z
updated: 2026-06-30T16:52:21Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-014
children: []
order: 18432
reporter: null
assignee: null
acceptance_criteria:
  - Per-customer 'conversion path' view shows the target Stewardship Level, the current stage (Suggested → Proposed → Owner Assigned → Qualified → Confirmed) and a requirement CHECKLIST for that target level.
  - A clear 'in the bag %' = checklist completion (qualifying questions + data capture + plan/sign-off) so anyone can see how close a customer is to their recommended bucket.
  - A team/manager worklist lists what is OUTSTANDING to convert each customer to its recommended bucket (sortable by % / value / level).
  - Progress is recorded by the owner (tick items / answer questions) and every change is logged (who/when) so it can't silently regress.
  - Reads the realised-vs-potential climb too (share of wallet), reflecting that a new high-scorer can be PROPOSED Strategic but only GRADUATES as realised £ accrues.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-process-flow.mmd
    role: the conversion path / stages this visualises
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: level_status state machine (5.1) + my-accounts surfacing (6.7)
  - path: backend/views/fast-track-lane/index.php
    role: read-only grid pattern + popover/checklist UI to reuse
relates:
  - T-0457
  - T-0496
  - T-0495
  - T-0493
  - T-0491
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 7
---

## Problem
We need to *see* where a customer is on their climb from current bucket to proposed bucket, and exactly what is
outstanding to complete it — so the team can act and managers can spot stalls.

## Terminology (the split — see P-0018-phase2-terminology.md)
This visualises **Account Elevation** — a **customer-level** journey up the Stewardship Levels (Suggested → Proposed →
Owner Assigned → Qualified → Confirmed). **"In the bag %" is the elevation progress, and belongs to the customer, not
to a single quote.** It is NOT the per-quote **Conversion Process** (System f/u · Quick · In-depth · Lifetime), which is
a separate quote-level concept (T-0496).

## Design notes
Per customer render: **target level** vs current + current **stage**; a **requirement checklist** for the target level
(qualifying questions + data capture + plan/sign-off) with **% complete = 'in the bag'**; and the **value climb**
(realised vs potential / share of wallet) alongside (graduation also needs realised £). A **manager worklist**
aggregates "what's outstanding" across customers. Owner ticks items / answers questions → advances the stage; all
changes logged (`customer_account_level_log`). Reuse the fast-track-lane read-only grid + popover/checklist patterns.

## Depends on / relates
T-0457 (state machine + storage), T-0496 (per-level checklists/questions), T-0495 (qualify writes progress).

## Originally
Filed as *"Create a way for the user to record progress on the process so we can visualise where a particular customer
is along their conversion path"* — renamed to **Account Elevation tracker** to match the agreed terminology; same scope.