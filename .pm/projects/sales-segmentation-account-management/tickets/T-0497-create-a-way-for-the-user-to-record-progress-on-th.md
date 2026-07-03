---
id: T-0497
title: Account Elevation tracker — record + visualise a customer's climb to their proposed bucket ('in the bag %')
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:32:38Z
updated: 2026-07-03T01:23:33Z
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
version: 11
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

## Conversation

**2026-06-30 18:52 claude-code:** **2026-06-30 — "in the bag %" now updates live as you tick** (sandbox, uncommitted; see TS-004).

The progress %/bar previously only recalculated on save. It now updates in real time as boxes are ticked / text/date fields are filled — mirroring the server rule exactly (done = checkbox ticked OR a text/date item filled; Qualified ✓ appears once all mandatory items are met). A subtle "preview — Save progress to keep" hint shows when the live value differs from the last saved value, so it's clear it isn't persisted yet; the server recompute on save stays the authority. Pure client-side enhancement to the tracker — no new data source.

**2026-07-02 12:14 claude-code:** **2026-07-02 — Code committed + pushed** in commit **`cede0ffb`** (branch `p0018-sales-segmentation-design`, pushed to origin; direct commit, allow_commit was off).

This ticket's share: the Account Elevation tracker — `account_level_qualify` + the worklist/detail views that record and visualise a customer's climb to their proposed bucket with the **"in the bag %"**, now including a **live client-side preview** that updates as items are ticked (before save), mirroring the server rule exactly.

**2026-07-03 00:26 claude-code:** Status reconciliation (code audit vs ACs) — most of this ticket shipped in direct commit cede0ffb7 (branch p0018-sales-segmentation-design), but three ACs have concrete gaps, so the body should stop implying the tracker is complete.

BUILT:
- Worklist: backend/controllers/AccountLevelsController.php:26-57 + backend/views/account-levels/index.php — effective level (override-aware), Stage column, in-the-bag % bar, realised-12m / realised-LTV / potential / share-of-wallet, whale/big-fish flags; filter by level/flag/domain; sortable by value fields, level, score.
- Detail + qualify: AccountLevelsController.php:59-126 + views/account-levels/view.php — per-level stacked checklist (common/components/LevelRequirements.php), server in-the-bag % (progress() :61-76), Qualified when all mandatory done, live client-side preview mirroring the server rule (view.php:196-228), plus the override/correction panel with append-only history.
- Engine: console/controllers/AccountLevelController.php recompute seeds level_status='suggested', ladder bands on realised £, new-track caps at Incubation (graduation-on-realised is real). Migrations m260630_170000/180000/190000 exist (guarded; run-status per environment unverified; branch not merged to master).

OUTSTANDING:
1. Stage machine incomplete (AC1): 'proposed' and 'confirmed' are enum values nothing ever sets — there is no confirm action anywhere; and the per-customer detail view doesn't render the current stage at all (only the worklist row does, index.php:91-95).
2. Worklist gaps (AC3): not sortable by in-the-bag % (q_in_bag missing from sort attributes, AccountLevelsController.php:52) and it never lists WHAT is outstanding per customer — just the %.
3. No change audit for progress (AC4): actionQualify upserts latest state only; AccountLevelsController.php:117 rewrites every item's 'at' timestamp on every save, and only the last updated_by/updated_at survive — unticking an item is untraceable. The customer_account_level_log table named in this ticket's design notes was never created. (The append-only log exists only for overrides, not checklist progress.)

Also note: account-levels/* is not in backend allowActions, so non-admin sales users need an RBAC grant before they can use it.

**2026-07-03 01:23 claude-code:** Worklist enhancement (Austin's request, 2026-07-03): added a **Customer type filter** to /account-levels — All / Corporate (hide personal) / Personal only. Personal = ya_customers.customerType=1 (the head row); unknown/NULL types stay visible under the corporate filter rather than silently vanishing. Implementation: backend/controllers/AccountLevelsController.php (ctype param + ya_customers join) and backend/views/account-levels/index.php (dropdown in the filter bar). php -l clean; SQL verified read-only on the sandbox.

Live counts at time of adding: 20,044 home accounts = 11,181 corporate + 8,863 personal. Personal by level: System 8,835 / Incubation 26 / **Account 2** / Strategic 0.

Note for the threshold workshop: those 2 personal customers at Account level (≥£8k/12m realised) are concrete instances of open decision 11 ("can a high-value private client reach Account/Strategic, or be capped at Incubation?") — use the Personal-only filter to pull them up in the room.

Uncommitted in the working tree along with the T-0480/T-0484 batch (allow_commit off).
