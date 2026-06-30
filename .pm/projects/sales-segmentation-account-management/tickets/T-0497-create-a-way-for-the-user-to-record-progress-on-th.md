---
id: T-0497
title: Create a way for the user to record progress on the process so we can visualise where a particular customer is along their conversion path
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:32:38Z
updated: 2026-06-30T16:11:11Z
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
version: 5
---

## Problem
We need to *see* where a customer is along their path from current bucket to proposed bucket, and exactly what is
outstanding to complete the conversion — so the team can act and managers can spot stalls.

## Design notes
The **conversion path** is the `level_status` machine (Suggested → Proposed → Owner Assigned → Qualified → Confirmed).
Per customer render:
- **target level** (proposed) vs current, and the current **stage**;
- a **requirement checklist** for the target level (qualifying questions + data capture + plan/sign-off) with
  **% complete = 'in the bag'** (the progress bar);
- the **value climb** (realised vs potential / share of wallet) alongside, since graduation also needs realised £.
A **manager worklist** aggregates "what's outstanding" across customers. Owner ticks items / answers questions →
advances the stage; all changes logged (customer_account_level_log). Reuse the fast-track-lane read-only grid +
popover/checklist patterns. This is the visible half of T-0496's playbooks.

## Depends on / relates
T-0457 (state machine + storage), T-0496 (the per-level checklists/questions), T-0495 (qualify writes progress).