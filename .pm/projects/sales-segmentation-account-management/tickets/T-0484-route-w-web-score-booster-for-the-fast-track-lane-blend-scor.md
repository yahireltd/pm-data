---
id: T-0484
title: Route W — web-score booster for the fast-track lane (blend score-tier with quote value)
type: feature
state: triaged
created: 2026-06-30T11:46:39Z
updated: 2026-06-30T11:46:39Z
project: sales-segmentation-account-management
section: null
parent: T-0482
milestone: MS-002
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - RouteWBlender::evaluate(quoteTotal, tier, params) is a pure function returning {admit, route ('Q'|'W'|'Q+W'|null), priority}; thresholds tunable (move to the T-0479 param store); passes php -l + a unit test.
  - FastTrackService::candidates() admits a new-customer quote via Route Q (quoteTotal >= bigQuote) OR Route W (tier A & quoteTotal >= wFloorA; tier B & quoteTotal >= wFloorB), and orders the weekly cap by the blended tier-weighted priority; each admitted lead is tagged with its route.
  - Web-score/tier is read from customer_sales_scores by ya_customers.email_domain (T-0456/T-0480); unscored/webmail leads fall back to Route Q only with neutral priority — Route W NEVER lowers the bar for unscored or low-tier (C) leads, so it adds precision, not flooding.
  - fast-track-route-w/backtest reproduces the uplift vs Route-Q-only on the leakage-safe first-quote extract (quote-potential/export-training); the shipped thresholds are chosen from its precision/volume curve and recorded.
  - Shadow-mode only (no SLA/ownership/assignment change); the trigger runs off the live web quote-save path; all new PHP passes php -l and a dry-run lists the blended lane vs the Q-only lane.
out_of_scope:
  - SLA timers, ownership/claim/auto-assign, escalation (T-0482 P2)
  - the leads.php gold-star badge + scorecard why-popover (T-0482 P2)
  - a trained ML model — the web-score IS the firmographic signal; no model needed (supersedes T-0481's ML path)
  - writing salesID / any account-ownership change (lane stays steering-only)
code_anchors:
  - path: common/components/RouteWBlender.php
    role: the blend (admission + tier-weighted priority) — BUILT on branch p0018-sales-segmentation-design, pure/liftable
  - path: console/controllers/FastTrackRouteWController.php
    role: actionBacktest — BUILT; validates uplift on real first-quote data
  - path: common/components/FastTrackService.php
    role: integrate Route W into candidates() (dual-route admit + blended ORDER BY) — lives on branch t0482-fast-track-lane
  - path: console/controllers/FastTrackController.php
    role: wire the blended ranker into evaluate()/backtest
  - path: customer_sales_scores
    role: tier/score source (per-domain), joined via ya_customers.email_domain
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - segmentation
  - fast-track
  - yasystem
  - build
attention: null
version: 1
---

## Problem
The fast-track lane (T-0482 P1) admits a new-customer quote on **quote value alone** (Route Q: quoteTotal >= bigQuote). T-0482 explicitly deferred a **"Route W web-score booster"** because segment/score coverage didn't exist yet. It now does (T-0456: ~3,990 domains scored & segmented and growing), so we can blend the web-score in.

## Evidence — the score is a stronger signal than quote value
On the leakage-safe first-quote extract (6,853 new-customer first quotes; label = became a valuable **repeat** customer, realised £ excl. the quote's own conversion). The web-score is **blind to our spend**, so the relationship isn't circular:
- **AUC (predict £8k+/yr repeat):** quote value **0.60** vs web-score **0.74**. (At £3k: 0.59 vs 0.66.)
- **Within every quote-value band**, tier A realises ~2–4x tier C at the *same* quote size — i.e. the score adds signal quote value can't see.
- **Backtest (fast-track-route-w/backtest):** Route Q alone (£4k floor) = 165 admits (1.6/wk), catches **2 of 19** whales. **Blended Q+W** = 199 admits (1.9/wk), catches **4**; tuning the tier-A floor to £1,000 → 2.1/wk, **5 whales (~2.5x)**. Equal-volume, the blended *ranking* sorts more whales into the top slots (3 vs 2 in the top 165).

## Design (built)
`RouteWBlender::evaluate(quoteTotal, tier, params)`:
- **Admit** if Route Q (quoteTotal >= bigQuote=£4,000) **OR** Route W (tier A & >= £1,500; tier B & >= £3,000).
- **Priority** = quoteTotal x tier multiplier (A 2.5, B 1.5, C/unscored 1.0) — ranks the weekly cap.
- Tags route Q / W / Q+W. Unscored/webmail → Route Q only, neutral priority.

## Integration
Lift `RouteWBlender` into `FastTrackService::candidates()` (on branch `t0482-fast-track-lane`): add the Route-W OR-branch to the admission SQL/filter, join the tier from `customer_sales_scores` via `email_domain`, and `ORDER BY` the blended priority. Thresholds → T-0479 param store.

## Caveats
- Coverage is ~16% of first-quote domains today (but ~84% of the whales, since we scored top-by-spend first); Route W strengthens as the scoring sweep completes and new leads are scored live.
- Whale count is small (19 in 2y) so absolute precision is low — the win is the **2–2.5x relative recall** at ~the same weekly volume.
- The score is a 2026 snapshot applied to historical quotes (mild optimism); robust because it's blind to spend and the within-band separation holds. At serve time the live score is legitimately available.

## Status
`RouteWBlender.php` + `FastTrackRouteWController.php` built & validated on the sandbox (branch `p0018-sales-segmentation-design`, **not committed** — project policy). Next: integrate into FastTrackService + pick final thresholds from the full-coverage backtest after the scoring sweep.