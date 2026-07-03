---
id: TS-005
slug: account-elevation-v2-tunable-criteria-param-sets-what-if-sta
title: Account Elevation v2 — tunable criteria (param sets + what-if), stage machine, audit log, segment/score drill-downs
project: sales-segmentation-account-management
created: 2026-07-03T01:58:26Z
updated: 2026-07-03T02:02:59Z
decisions:
  - "Banding logic extracted into pure common/components/AccountLevelEngine.php — the ONLY implementation, shared by recompute + simulator. Parity proven: 0 mismatches across 20,044 accounts; refactored recompute reproduces 18,561/1,279/191/13 exactly (compute_version cal-mvp-3). Ratified as ADR-011."
  - 'Two NEW tables: account_level_param_sets (named/versioned threshold sets, is_live=1 drives recompute + page) and customer_account_level_log (append-only elevation audit: status changes, qualify progress, re-bands). What-if preview is read-only; "apply now" re-bands only changed rows, logs each level move, never touches confirmed_level/overrides/stages. Simulate reports counts AND £ flows (realised/12m + potential) per level plus named biggest movers.'
  - "Stage machine shipped with minimal safe semantics (full ownership gates await the workshop): Mark Proposed (from Suggested), Confirm Level (hard-gated on the qualify checklist's mandatory items; writes confirmed_level), Reopen (back to Suggested, clears confirmation) — all POST-only, all logged. Worklist/work-page UX: level tiles with £ sums, personal + unscored filters, segment shown under company with taxonomy drill-down (industry → company type → sub-type), score drill-down showing rubric components (fit/freq/scale), evidence, AND the financial picture (potential, realised, committed fwd, txns, spend-at-scoring with anti-leakage note). Collation standard set: domain-join columns align to customer_sales_scores.domain (utf8mb4_0900_ai_ci); migrations self-align — fixed live after Austin hit the general_ci mismatch on customer_account_levels."
  - "Shipped as commit ea2060ff2 (13 files, +1,382/−271), pushed to origin — committed at Austin's explicit direction, which is the human authorisation the allow_commit=false policy exists to require. Note: consider updating the project's agent_policy (or adding a per-stream branch note) to reflect this working mode, so future commits don't read as policy breaches."
actions:
  - text: "Ben (or delegate) reviews T-0479 + T-0497 — runs completed with step-by-step checklists; review queue now: T-0482, T-0480, T-0484, T-0479, T-0497."
  - text: "Austin: add the new tables to the nightly live→test copy exclusion list — warehouse_settings, fast_track_leads, segment_profile, customer_account_levels, account_level_qualify, customer_account_corrections, account_level_param_sets, customer_account_level_log + view v_customer_segment_scores; consider excluding customer_sales_scores so the 8,645 scored rows survive the restore."
  - text: "Before any recompute cron or post-confirmation recompute: make account-level/recompute preserve confirmed_level + level_status (currently truncate-rebuild wipes them — escalated on T-0457, fold into the T-0457a engine slice)."
    ticket: T-0457
  - text: 'Commit the working tree on p0018-sales-segmentation-design (allow_commit off — Austin commits): ~16 files across T-0480/T-0484 finishing work, IdempotentMigrationTrait recreation, AccountLevelEngine + param-sets/log migration, both account-levels pages, personal/unscored filters, drill-downs. Suggested message: "P-0018/T-0479+T-0480+T-0484+T-0497: tunable banding engine + param sets, stage machine + audit log, email_domain sync, RouteWBlender tests".'
side_quests: []
problem: "The account-levels pages were a read-only MVP: banding thresholds were hardcoded (blocking the workshop from experimenting), the 5-stage elevation machine had two unreachable stages (nothing could ever be proposed or confirmed), progress changes were untraceable (no audit log), personal/unscored accounts cluttered the worklist, and neither the score nor the segment explained themselves. This session (claimed runs on T-0479 + T-0497) built the tunable-criteria layer and finished the tracker."
success_criterion: A manager can tune the banding criteria on the page and instantly see how many accounts AND how much realised/potential £ would move (no writes), save/make-live/apply named threshold sets with per-account logging; the stage machine works end-to-end (propose → qualify → confirm, guarded + logged); the worklist filters personal/unscored and drills into score components and the segment tree; recompute and simulator provably share one engine (parity = 0 mismatches over 20,044 accounts).
phase: build
version: 6
---

# TS-005: Account Elevation v2 — tunable criteria (param sets + what-if), stage machine, audit log, segment/score drill-downs

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
