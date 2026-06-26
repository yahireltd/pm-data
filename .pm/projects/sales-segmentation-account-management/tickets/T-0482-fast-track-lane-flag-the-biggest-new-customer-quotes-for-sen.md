---
id: T-0482
title: Fast-track lane — flag the biggest new-customer quotes for senior attention (shadow MVP → SLA queue)
type: feature
state: in_progress
created: 2026-06-26T22:23:18Z
updated: 2026-06-26T22:44:42Z
project: sales-segmentation-account-management
section: null
parent: T-0457
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
  - Reversible migration creates `fast_track_leads` (per-customer lifecycle table) and adds `quotes.fasttrack_*` snapshot columns; `./yii migrate` applies and `migrate/down` reverts cleanly on the sandbox.
  - "`FastTrackService::evaluate()` flags a quote iff: scope (active, forward-dated, not-cancelled, resolves to a NEW/unowned customer — owned = real salesID + realised history are excluded) AND Route Q (quoteTotal >= threshold); deduped to one entry per customer (corporate=email_domain key, webmail=individual-email key); admitted up to a weekly cap ranked by quote value."
  - Console `fast-track/evaluate` runs in SHADOW mode (writes/upserts `fast_track_leads` + stamps `quotes.fasttrack_*`, NO SLA/ownership/assignment); `fast-track/evaluate --dryRun=1` is read-only and prints what would be flagged.
  - Console `fast-track/backtest` reproduces the precision/recall-vs-known-whales numbers from the design doc on the real first-quote history.
  - All thresholds (BIGQUOTE, WEEKLY_CAP, webmail blocklist) live in one place, ready to move to the T-0479 param store; no magic numbers scattered in queries.
  - Trigger computes in a console job OFF the live web quote-save path (zero risk to the conversion path).
  - New PHP passes `php -l`; dry-run verified against real sandbox data and the flagged set matches the backtest expectation.
out_of_scope:
  - SLA timers, ownership/claim/auto-assign, escalation ladder (P2)
  - capacity cap + volume governor (P2)
  - the leads.php gold-star badge + scorecard 'why' popover UI (P2)
  - Route W web-score booster — needs segment/score coverage, T-0456/T-0473-4 (P2)
  - account-level unify / home_customerID / WON→suggest-confirm (P3)
  - writing salesID or any change to account ownership (lane is steering-only)
code_anchors:
  - path: console/migrations/
    role: "new migration: create fast_track_leads + add quotes.fasttrack_* cols (safeUp/safeDown)"
  - path: common/models/FastTrackLeads.php
    role: new ActiveRecord + query class (active/byOwner/etc)
  - path: common/components/FastTrackService.php
    role: "new: evaluate() trigger+scope+dedup+cap logic"
  - path: console/controllers/FastTrackController.php
    role: "new: actionEvaluate (shadow + --dryRun), actionBacktest"
  - path: docs/p0018-sales-segmentation/P-0018-fast-track-lane.md
    role: design doc + backtest (the build spec)
  - path: src/quote_potential/scorecard.py
    role: reference (relegated to why-display; NOT the trigger)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260626-2224
    model: claude-opus-4-8
    started: 2026-06-26T22:24:50Z
    status: in_progress
    policy_ack:
      branch: null
      branch_source: null
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-26T22:24:50Z
    progress:
      - at: 2026-06-26T22:44:42Z
        note: "P1 shadow-mode pipeline BUILT on branch t0482-fast-track-lane (NOT committed — project policy blocks agent commit/push; awaiting human). Files: migration m260626_230000_create_fast_track_leads_table.php (fast_track_leads + quotes.fasttrack_* cols, reversible); common/models/FastTrackLeads.php; common/components/FastTrackService.php (candidates() trigger: quote-value + new/unowned scope via no-prior-delivered-contract + per-customer dedup [domain, or individual email for webmail] + weekly cap; evaluate() shadow upsert); console/controllers/FastTrackController.php (actionEvaluate --dryRun + shadow; actionBacktest read-only). All 4 pass php -l (MAMP php8.2). VALIDATED the live trigger via --dryRun against the sandbox DB: returned 25 new-customer big quotes (mavericklive £28.5k, alexandrapalace £20k, sleekevents £17.5k...), webmail correctly keyed per-individual + tagged, deduped, ranked by value — ~2/week at £4k floor (very selective; threshold tunable). Backtest perf-fixed (added quote prefilter) and re-running. NOT yet done: migration not run on sandbox (would alter shared test schema — left for human), shadow write not exercised, leads.php badge is P2."
labels: []
attention: null
version: 4
---

## Problem
New high-potential customers' first quotes sit in the normal ~66/week pile and don't get timely senior attention. We want a **fast-track lane**: a short, owned, time-bounded list so a senior rep contacts a promising new lead within hours.

## Context — what the data forced (see docs/p0018-sales-segmentation/P-0018-fast-track-lane.md)
- An empirical backtest on the real 6,898 first-quotes **disproved the "scorecard-fed" premise**: ranking by the scorecard's `potential` catches **0 of 33 whales in the top 500** (worse than random). **Plain quote value is the best signal** (top-50 → 7/33 whales, 14% precision; top-500 → ~52% recall). So the trigger is **quote value** (+ web-score tier-A booster when available), and the scorecard is relegated to the "why" display.
- Honest scope: this is "senior eyes on the biggest new quotes," not a hidden-whale detector (the ML model was validated as non-viable — T-0481). Precision-first; a missed marginal lead stays in the normal leads queue, a flooded lane is the failure.

## Approach (phased; this ticket delivers P1, body documents P2/P3)
- **P1 (this run): shadow-mode pipeline.** `fast_track_leads` table + `quotes.fasttrack_*` snapshot cols; `FastTrackService::evaluate()` (quote-value trigger + new/unowned scope + per-customer dedup + weekly cap); console `fast-track/evaluate` (shadow: writes entries, no SLA/assignment) + `--dryRun` preview + `fast-track/backtest`. Run in shadow for a few weeks to measure real precision before any senior-capacity commitment.
- **P2:** the `fast-track-leads.php` page, senior-pool RBAC, claim + 60-min auto-assign, SLA-on-entry + resolution-SLA + escalation, capacity cap + volume governor, manager board, tune-on-outcome report, the `leads.php` gold-star badge + scorecard "why" popover, Route W (web-score) once segment/score coverage is confirmed.
- **P3:** unify into the T-0457 account-level front door (home_customerID, WON → suggest→confirm, big-fish inbox sub-filter).

## Dependencies
T-0480 (normalised email_domain — stubbed inline for P1), T-0479 (param store — hard-coded thresholds for P1), T-0457 (account model — P3), T-0456/T-0473-4 (customer scored+segmented — needed for the Route W web-score booster).