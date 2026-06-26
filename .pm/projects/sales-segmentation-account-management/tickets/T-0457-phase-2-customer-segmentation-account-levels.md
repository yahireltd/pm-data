---
id: T-0457
title: Phase 2 · Account levels & assignment — confidence-weighted blend + suggest→confirm workflow
type: feature
state: triaged
created: 2026-06-22T21:41:39Z
updated: 2026-06-26T14:50:41Z
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
  - Every active customer carries a system-computed suggested_level (system / incubation / account / strategic), recomputed nightly, from the confidence-weighted blend of realised value (ya_contracts, contractTotal inc-VAT) and potential (score + segment + deduped quoted demand), with the new-vs-existing weight (alpha) applied and guardrails enforced (no-realised-history is capped at Incubation; Strategic respects a per-senior-AM capacity cap).
  - Quoted demand is deduped to opportunities (customer + hireStartDate +/-2 days; £0 and internal yahire.com quotes excluded; copiedQuote flag NOT used) and a win-rate (realised/quoted) metric flags quotes-a-lot/wins-little conversion-problem customers.
  - "A suggest->confirm workflow exists: a human can propose / confirm / override the level (mandatory reason, logged to an audit table), ownership is assigned via salesID, and each level has its ownership-requirement gate that must be met before 'confirmed'."
  - Per customer the screen shows actual value (ya_contracts), potential value (model) and the potential-minus-realised gap; filters/queries use effective_level = COALESCE(confirmed_level, suggested_level); the level surfaces on my-accounts and a new manager screen.
  - A quarterly transfer-window review worklist runs (suggested != confirmed, lapsed, split-ownership, white-whale) with a named owner + cadence.
  - The new-vs-existing test uses real ya_contracts history (a delivered, unconverted=0, contractType=1 contract), NOT ya_customers.totalContracts.
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
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
---

## What this is

The **account-level layer** of P-0018: give every customer one of four stewardship levels — **System-only / Incubation / Account / Strategic** — and the **suggest→confirm workflow** by which a human turns a system suggestion into a real, *owned* account at that level. It consumes the **score** ([[T-0456]]) and **segment** ([[T-0473]]) plus a new **quoted-demand** signal, and is the concrete home of the "Conversion Process" the original sales flow left vague.

Full written design (10 sections + flow diagram): `~/Documents/P-0018-account-level-design.md`, `P-0018-account-level-flow.{drawio,mmd}`. Decisions + sandbox calibration: **TS-003**. Canon: ADR-007/008/009. Tuning + what-if simulation: [[T-0479]].

## The model — confidence-weighted blend

Three distinct layers: **segment** (who) / **score** (potential) / **account-level** (stewardship — this ticket). Account-level sits **beside, never replaces** the existing `tierID` value-grade and `accountType` billing type.

Proposed level comes from a **confidence-weighted blend**, not a hard new/existing split:

`expected_value = α · realised_value + (1 − α) · potential_value`

- **realised_value** — annualised hire-only revenue from `ya_contracts` (`contractTotal` inc-VAT, `contractType=1`, `unconverted=0`). *Do NOT use `ya_customer_stats.contractsValue` — it is ex-VAT `contractPrice`.*
- **potential_value** — score→£ curve × segment fit, **or** revealed demand from **deduped annualised quoted value** (`quotes.quoteTotal`); revealed demand is trusted over the cold score when present.
- **α (evidence weight, 0–1)** — how much realised history exists (delivered-order count, tenure, recency). New customer α≈0 → driven by score + segment + a weighted dab of the **initial quote value (β)**; established repeat α≈0.8–0.9 → history dominates, score only nudges. α *earns itself* as orders land. The exact curve is TUNABLE → [[T-0479]].

**Map to levels** via blended value + the **potential−realised gap**: low/low → System-only; **high potential + low realised (big gap) → Incubation / white-whale**; high realised → Account; high realised + high potential + repeat cadence → Strategic. Guardrails: a customer with **no realised history cannot be born Account/Strategic** (a consequence of α≈0 + the human gate); **Strategic respects a per-senior-AM capacity cap**.

**Quoted demand + win-rate.** Quoted value is deduped to opportunities — **customer + `hireStartDate` ±2 days** (the existing `UsefulFunctions` convention; the `copiedQuote` flag is unused/£0 so not relied on; £0 and internal `yahire.com` quotes excluded). **win-rate = realised ÷ quoted** flags "quotes-a-lot / wins-little" conversion-problem customers — e.g. **customer 8701: 132 opportunities, 4 won (3%), £69.6k quoted, ~£2k realised**: a realised-only model wrongly dumps them in System-only; the blend correctly elevates them to a high-priority Incubation gap.

## Conversion workflow (the "Conversion Process" made concrete)

A suggest→confirm state machine per customer: `suggested → proposed → owner_assigned → requirements_met → confirmed` (+ rejected / demote). `effective_level = COALESCE(confirmed_level, suggested_level)`.
- **System-only** — auto (suggested == confirmed); a named pool **steward** owns the pool, not individuals.
- **Incubation** — owner = SE / junior AM; gate = segment + key contact captured (customerType-aware: personal / no-domain uses the same set minus `companyType`).
- **Account** — owner = AM; gate = plan approved by a manager.
- **Strategic** — owner = senior AM; gate = bespoke plan + manager sign-off + a capacity slot.

**Transfer windows** (quarterly): a review worklist of suggested≠confirmed, lapsed, split-ownership, white-whale. **Big-fish**: real-time manager alert on a large new opportunity (level still capped at Incubation until realised).

## Calibrated starting numbers (sandbox, 2026-06-26 — all TUNABLE via [[T-0479]])
- Band cuts: **System < £8k · Account ≥ £8k · Strategic ≥ £40k** realised/yr → ≈171 Account, ≈22 Strategic (staffable). 86% of active customers are < £2k/yr.
- Quote dedup ≈ 15% of rows (27,393 → 23,395 opportunities last 12m).

## Data model
- New `customer_account_levels` (1 row per active customer; the **home row** per email-domain economic account carries the level + rolled values, satellites NULL) + append-only `customer_account_level_log`.
- New nightly recompute `console/controllers/AccountLevelController::actionRecompute`.
- New/existing test uses **real `ya_contracts` history**, NOT `ya_customers.totalContracts` (unreliable — established quoters show `totalContracts=0`).
- Reuse `salesID` (owner), `keyAccount`/`vip`; keep `tierID` / `accountType` distinct.
- Surfaces: extend `backend/views/sales/my-accounts.php` (level badge + confirm/override) + a new manager screen `AccountLevelsController`.

## Build sequencing
- **Phase 0** — indexed `ya_customers.email_domain`; `customer_sales_scores.classification` ([[T-0456]]).
- **Phase 1 (MVP)** — tables + recompute + my-accounts badge + confirm/override + Strategic RBAC & capacity cap.
- **Phase 2** — full state machine + ownership gates + transfer-window job + big-fish mailer.
- **Phase 3** — segment enrichment-on-demand ([[T-0473]]/T-0474/T-0475).
- **Phase 4** — the (currently unbuilt) System-only nurture engine + named steward.

## Open decisions (workshop)
£ thresholds; the α curve + β; ownership-requirement sets; the Strategic capacity number; the named steward (+ who funds Phase-4 nurture); the domain-ownership cascade rule — full 18 in TS-003 / design doc §9.