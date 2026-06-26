---
id: T-0457
title: Phase 2 · Account levels & assignment — confidence-weighted blend + suggest→confirm workflow
type: feature
state: triaged
created: 2026-06-22T21:41:39Z
updated: 2026-06-26T20:44:00Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-002
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Every active customer carries a system-computed suggested_level, recomputed nightly by a SHARED pure engine, from the confidence-weighted blend (alpha-weighted realised_blend + quote nudge + potential). expected_value only STEERS; the level is BANDED on a realised £ FLOOR (realised_gate), never on alpha.
  - Account requires realised_gate >= floor AND a graduation test (so a single first delivered order graduates via Incubation, not straight to Account); Strategic requires realised_gate >= floor AND a repeat_signal; both read DELIVERED hire revenue (hireEndDate in the past), never a forward-dated/converted-but-undelivered booking.
  - "Guards enforced: lapsed-high-tier (Diamond/Gold/VIP/keyAccount/high-lifetime never silently dropped to System), committed_fwd demote-exemption, and the per-senior-AM Strategic capacity cap at confirm."
  - Quoted demand is deduped to opportunities (customer + hireStartDate +/-2 days; MAX of one engaged live quote not SUM; £0 + internal yahire.com excluded); a win-rate (realised/quoted) flags quotes-a-lot/wins-little customers; big-fish alerts read the RAW uncapped quote.
  - "A suggest->confirm workflow exists: propose/confirm/override (mandatory reason, logged to audit; compute_version stamped on every write path), ownership via salesID, per-level ownership gate before 'confirmed'; effective_level = COALESCE(confirmed, suggested)."
  - Per customer the screen shows realised (actual), potential, and the gap; the level surfaces on my-accounts + a new manager screen; a quarterly transfer-window worklist runs with a named owner + cadence.
  - New-vs-existing uses real ya_contracts delivered history, NOT ya_customers.totalContracts; realised queries exclude archived/dead-status rows.
  - Segment (industry/company-type, from ADR-006/007) is a FIRST-CLASS input via a tunable per-segment profile — a repeat_likelihood prior feeding the starting alpha + the Quick-vs-Lifetime conversion route, a segment-aware potential multiplier/curve, optional band-threshold overrides, and the industry-specific qualification set — NOT just a flat fit multiplier. classification (mainstream/trade/disqualified) routes trade->trade lane and disqualified->System.
  - The web-lookup score + classification cover new leads too (esp. webmail/personal with no domain match handled via the quote-value proxy); ~39% of inbound quotes scored disqualified and ~24% A-tier on the 137-domain sample — the disqualified gate is a primary efficiency lever.
out_of_scope: []
code_anchors:
  - path: common/models/YaCustomers.php
    symbol: salesID / keyAccount / vip / tierID / changeAccountManager()
  - path: common/models/YaContracts.php
    symbol: contractTotal, contractType, unconverted, hireStartDate (realised value)
  - path: common/models/Quotes.php
    symbol: quoteTotal, status, customerID, hireStartDate (quoted demand + dedup)
  - path: common/models/YaCustomerStats.php
    symbol: updateCustomerStats() — do NOT reuse contractsValue (ex-VAT)
  - path: backend/views/sales/my-accounts.php
    symbol: level badge + confirm/override surface
  - path: backend/controllers/SalesScoresController.php
    symbol: pattern to mirror for AccountLevelsController
  - path: console/migrations/
    symbol: new customer_account_levels + customer_account_level_log migrations
relates:
  - T-0456
  - T-0473
  - T-0479
blocks: []
blocked_by:
  - T-0480
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 6
---

## What this is

The **account-level layer** of P-0018: give every customer one of four stewardship levels — **System-only / Incubation / Account / Strategic** — and the **suggest→confirm workflow** by which a human turns a system suggestion into a real, *owned* account at that level. It consumes the **score** ([[T-0456]]), **segment** ([[T-0473]]) and a **quoted-demand** signal, and is the concrete home of the "Conversion Process" the original sales flow left vague.

**Design docs:** base = `~/Documents/P-0018-account-level-design.md`; **refinement = `~/Documents/P-0018-account-level-blend-addendum.md`** (the confidence-weighted blend, the parameter store, the what-if tool); flow = `P-0018-account-level-flow.{drawio,mmd}`. Decisions + sandbox calibration: **TS-003** (12 decisions). Tuning + simulation: [[T-0479]]. Hard prerequisite: [[T-0480]] (`email_domain` plumbing).

## The model — confidence-weighted blend (REFINED)

Three layers: **segment** (who) / **score** (potential, blind to spend) / **account-level** (stewardship — this ticket). Account-level sits **beside, never replaces** the existing `tierID` value-grade and `accountType` billing type. Scoring is the **prior for the whole base** (existing customers are scored too); new-vs-existing is just *how much weight* the score carries.

A **steering value** blends the three signals, weighted by the customer's own evidence:

`expected_value = α·realised_blend + γ·quote_signal + (1 − α − γ)·potential_value`

- **α (evidence weight, 0–0.90)** from a recency-decayed **delivered**-order count + NULL-safe tenure → α≈0 for a new lead (potential + a small quote nudge drive it), α≈0.7–0.9 for an established repeat (history dominates, score only nudges). Earns itself order-by-order.
- **potential** = blind score→£ curve × segment fit (NULL→0 inside the blend; segment multipliers ship neutralised to 1.0 until enrichment lands).
- **quote_signal** = MAX of **one** genuinely-engaged live quote (not SUM — resists padding), capped £30k + annualised.

**CRITICAL — banding is on a realised £ FLOOR, not α.** `expected_value` only *steers* (scatter axis, queue ranking, big-fish trigger); it never confers a level. The level gate reads ground-truth realised £, with **two named realised bases** (both pure `contractTotal`): `realised_blend` (recency-smoothed) for steering, `realised_gate` (multi-year annualised) for the hard floors — so a biennial £80k venue annualises to £40k and isn't demoted in its off-year.

Ladder (first match wins; all TUNABLE via [[T-0479]]):
- **0** `classification` disqualified→System / trade→trade-lane (held behind a manual competitor-exclusion checkpoint until the column exists — [[T-0456]]).
- **1 Lapsed-high-tier guard:** a dormant Diamond/Gold/VIP/keyAccount/£50k-lifetime account is **never silently dropped to System** → `lapsed_review`, hold prior level.
- **2 Strategic** (suggest): `realised_gate ≥ £40k` AND repeat_signal (`txn_12m≥2` OR ≥2 distinct order-years).
- **3 Account:** `realised_gate ≥ £8k` AND a **graduation test** (txn≥2 OR ≥2 years OR tenure≥9m OR lifetime≥£20k with a **DELIVERED** hire) — so a single first order graduates through Incubation, never straight to Account.
- **4 Incubation:** `realised_gate < £8k` AND high potential (`≥£15k` or white-whale) — the gap; the white-whale nursery + the high-potential new lead land here.
- **5 Triage/System:** unscored personal lead with a live quote → named triage queue (owner + SLA), not a silent System sink; pure dead weight → System.

`committed_fwd` demote-exemption (a material forward book holds the level + fires big-fish). Three barriers between potential and a senior tier: **α** (shrinks steering value), the **realised floor + graduation** (band gate), the **human confirm + per-senior-AM capacity cap**.

**Quoted demand + win-rate.** Deduped to opportunities — customer + `hireStartDate` ±2 days (the existing `UsefulFunctions` convention; `copiedQuote` flag unused/£0; £0 + internal `yahire.com` excluded). **win-rate = realised ÷ quoted** flags conversion-problem customers — e.g. **customer 8701: 132 opportunities, 4 won (3%), £69.6k quoted, ~£2k realised** → a realised-only model dumps them in System; the blend elevates them to a high-priority Incubation gap.

## Conversion workflow

`suggested → proposed → owner_assigned → requirements_met → confirmed` (+ rejected/demote); `effective_level = COALESCE(confirmed_level, suggested_level)`. System-only auto (pool steward owns the pool); Incubation → SE/junior AM + segment & contact captured (customerType-aware); Account → AM + manager-approved plan; Strategic → senior AM + bespoke plan + sign-off + capacity slot. Quarterly **transfer-window** worklist (suggested≠confirmed / lapsed / split-ownership / white-whale). Real-time **big-fish** alert (reads the RAW quote; level still capped until realised).

## Calibrated starting numbers (sandbox 2026-06-26 — TUNABLE)
Band cuts System<£8k / Account≥£8k / Strategic≥£40k → ≈171 Account, ≈22 Strategic (staffable); 86% of active customers <£2k/yr; quote dedup ≈15%.

## Data model
New `customer_account_levels` (home-row per email-domain economic account; + α/γ/n_eff/realised_blend/realised_gate/expected_value/gap/status_stage/conversion_process columns) + append-only `customer_account_level_log`. Recompute `console/controllers/AccountLevelController::actionRecompute` calls the shared pure `AccountLevelEngine::computeRow()`, applies hysteresis/dormancy/demote-exemption (prior-state), stamps `compute_version`, logs material deltas only. Reuse `salesID`/`keyAccount`/`vip`; keep `tierID`/`accountType` distinct; new/existing uses real `ya_contracts` history NOT `totalContracts` (unreliable). Realised query excludes `archived`/dead status. Surfaces: `my-accounts.php` badge + confirm/override; new `AccountLevelsController` manager screen.

## Build sequencing (sub-tasks suggested)
T-0457a engine + param store; **T-0480** `email_domain` plumbing (predecessor); T-0457b recompute + fact-frame; T-0457c simulator UI ([[T-0479]]); T-0457d promote/RBAC/audit. Then Phase 3 segment enrichment ([[T-0473]]); Phase 4 the (unbuilt) System-only nurture engine + named steward.

## Open decisions (workshop)
£ thresholds; α curve (k/α_max/H/T0) + matrix-vs-smooth + tenure basis; graduation thresholds; lapsed/demote floors; Strategic capacity number; named steward (+ Phase-4 nurture funding); domain-ownership cascade; scoring-refresh SLA & cost. Full 16 in the addendum §H + base doc §9 — captured in TS-003.

## Conversation

**2026-06-26 20:44 claude-code:** **Update (26 Jun)** — added a **Grow** view to the account-level demo. It's the *same four levels* and the *same model*, just banded on a customer's **potential** instead of their realised spend — so it surfaces *where the growth is* (under-served accounts we could win more from) rather than *who's biggest today*. Already-won accounts (we hold ~80%+ of their wallet) are flagged "captured" and step aside. It's a presentation lens over the existing banding engine — no change to the model itself.

Design note: `docs/p0018-sales-segmentation/P-0018-defend-grow-lens.md`; full design + the rejected alternatives are in TS-003 (decision #24). See **T-0479** for the important caveat that the demo's parameters and the "potential" figure are still provisional/uncalibrated. Branch `p0018-sales-segmentation-design`, commit 9c45b3ce1.
