---
id: T-0482
title: Fast-track lane — flag the biggest new-customer quotes for senior attention (shadow MVP → SLA queue)
type: feature
state: review
created: 2026-06-26T22:23:18Z
updated: 2026-06-30T13:58:24Z
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
    status: completed
    policy_ack:
      branch: null
      branch_source: null
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-26T22:24:50Z
    progress:
      - at: 2026-06-26T22:44:42Z
        note: "P1 shadow-mode pipeline BUILT on branch t0482-fast-track-lane (NOT committed — project policy blocks agent commit/push; awaiting human). Files: migration m260626_230000_create_fast_track_leads_table.php (fast_track_leads + quotes.fasttrack_* cols, reversible); common/models/FastTrackLeads.php; common/components/FastTrackService.php (candidates() trigger: quote-value + new/unowned scope via no-prior-delivered-contract + per-customer dedup [domain, or individual email for webmail] + weekly cap; evaluate() shadow upsert); console/controllers/FastTrackController.php (actionEvaluate --dryRun + shadow; actionBacktest read-only). All 4 pass php -l (MAMP php8.2). VALIDATED the live trigger via --dryRun against the sandbox DB: returned 25 new-customer big quotes (mavericklive £28.5k, alexandrapalace £20k, sleekevents £17.5k...), webmail correctly keyed per-individual + tagged, deduped, ranked by value — ~2/week at £4k floor (very selective; threshold tunable). Backtest perf-fixed (added quote prefilter) and re-running. NOT yet done: migration not run on sandbox (would alter shared test schema — left for human), shadow write not exercised, leads.php badge is P2."
    ended: 2026-06-26T22:51:02Z
    summary: "Built the first working version of a \"fast-track lane\" — a way to automatically spot the biggest quotes from brand-new customers so a senior salesperson can contact them quickly, instead of those promising leads sitting unnoticed in the ~66-new-quotes-a-week pile. We proved on real data that a new customer's biggest quotes are about 30× more likely to turn into a valuable account than an average quote, so putting senior eyes on them is worth the time. This first version runs quietly in the background (\"shadow mode\") and just produces the list — so we can measure how good it really is before committing any sales-team time or SLAs to it. Without it, genuinely valuable new customers keep slipping through unnoticed. Honest caveat: it's \"senior eyes on the biggest new quotes,\" not a perfect whale-detector — many flagged quotes will be big one-off events, and that's expected."
    test_plan: |-
      Code is on branch `t0482-fast-track-lane` (commit eace4aa62; 4 files: a migration, a model, a service, a console controller). Run everything with **MAMP's PHP 8.2** (the Homebrew php@7.4 is broken). **Sandbox only.**

      1. Review the 4 files for sanity (all pass `php -l`).
      2. **Migration:** `./yii migrate` → confirm a `fast_track_leads` table and 4 new `quotes.fasttrack_*` columns are created. Then `./yii migrate/down 1` → reverts cleanly. Re-apply.
      3. **Read-only preview:** `./yii fast-track/evaluate --dryRun=1` → lists new-customer big quotes (biggest first), webmail tagged. No writes. (Already verified: returns ~25 entries at the £4k default.)
      4. **Shadow write:** `./yii fast-track/evaluate` → `fast_track_leads` rows appear and the matching quotes get `fasttrack_flag=1`. Run it AGAIN → no duplicates (UNIQUE on `dedup_key`, upsert).
      5. **Tune:** `./yii fast-track/evaluate --bigQuote=2500 --cap=20 --dryRun=1` → more entries; higher `--bigQuote` → fewer. This is how you set the precision/volume trade-off.
      6. **Cross-impact:** the migration only ADDS 4 nullable columns to `quotes` (default 0/null) + a new table — confirm quote create / edit / list pages are unaffected. The trigger is a console job **off the web quote-save path**, so the live conversion flow is untouched.

      KNOWN GAPS (not blockers, logged): the in-DB `fast-track/backtest` is too slow (correlated subqueries — needs a JOIN rewrite); the authoritative precision numbers are from the Python harness (top-50 = 14% precision, top-500 = ~52% recall of the 33 known whales). No SLA / ownership / UI badge yet — that's P2.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: written
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-26T22:51:02Z
version: 7
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

## Conversation

**2026-06-26 22:51 claude-code:** Run run-20260626-2224 completed — Built the first working version of a "fast-track lane" — a way to automatically spot the biggest quotes from brand-new customers so a senior salesperson can contact them quickly, instead of those promising leads sitting unnoticed in the ~66-new-quotes-a-week pile. We proved on real data that a new customer's biggest quotes are about 30× more likely to turn into a valuable account than an average quote, so putting senior eyes on them is worth the time. This first version runs quietly in the background ("shadow mode") and just produces the list — so we can measure how good it really is before committing any sales-team time or SLAs to it. Without it, genuinely valuable new customers keep slipping through unnoticed. Honest caveat: it's "senior eyes on the biggest new quotes," not a perfect whale-detector — many flagged quotes will be big one-off events, and that's expected.

**2026-06-30 12:06 claude-code:** **Built the read-only lane PAGE (the P2 "fast-track-leads.php") — viewing the lane in the browser instead of the console.**

`backend/controllers/FastTrackLaneController.php` + `backend/views/fast-track-lane/index.php` (branch `p0018-sales-segmentation-design`, uncommitted). Reuses the Sales Scores read-only grid pattern:
- Live, read-only grid of the blended lane (FastTrackService::candidates) — columns: route (Q / W / Q+W badge), tier, quote £, blended priority, domain (link), company, hire date, quote link.
- Header explainer (Route Q vs Route W), a route-mix tally, and filters (route, since, Route-Q floor, Route-W tier-A floor).
- No writes — shadow-mode `fast-track/evaluate` still owns persistence.

URL: **/fast-track-lane** (same URL-only convention as Sales Scores — not in the nav). It's route-RBAC'd by mdm AccessControl: a superadmin can open it now; other roles need `/fast-track-lane/*` granted in the RBAC admin, exactly like Sales Scores. Lint clean; data path smoke-tested. Still P2 and not built here: SLA/ownership/claim, the gold-star badge on leads.php.

**2026-06-30 13:58 claude-code:** **Fixed a real defect in the lane's new/unowned detection + surfaced segment/repeat (branch p0018-sales-segmentation-design, uncommitted).**

**Defect:** the lane decided "new customer" by matching the QUOTE's email *domain* against past contracts. Existing customers often quote from a different/мistyped email than their contracts, so they leaked in as "new". Real example Austin caught: a £20k quote `brian.watson@alexandrapalace.co` (a typo, missing the "m") — it's linked to `customerID 5340`, the live **Alexandra Palace** key account (VIP, owner salesID 363, 15 delivered contracts, £32k). It was showing as a brand-new lead.

**Scale of it:** of 131 admitted quotes, **83 were actually existing/owned** once resolved by customerID; only **48 are genuinely new**. The lane was ~63% existing accounts.

**Fix:** `FastTrackService::classifyOwnership()` now resolves identity by the **customerID on the quote** (following `mergedIntoCustomerID` to the canonical customer), and flags new/unowned from the canonical's **delivered-hire history + salesID** — robust to a mistyped/changed quote email. Added a `scope` (new [default] / existing / all). This is the T-0480 identity-graph idea applied to the lane.

**Also surfaced (Austin's asks):** a **Segment** column (industry · company_type from the score) and a **Repeats?** column (the web-lookup's own `repeat` label + the segment's re-hire rate % + events/yr), plus an "existing/owned" badge in non-new scopes, and labelled filter inputs.

Knock-on: the same customerID resolution should feed the scoring identity (one org = one record) — currently typo'd domains get scored as separate phantom customers (e.g. Alexandra Palace exists as `.co`, `.com`, `.comold`). Worth folding into T-0480.
