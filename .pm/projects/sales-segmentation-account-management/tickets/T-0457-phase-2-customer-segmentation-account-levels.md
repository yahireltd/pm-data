---
id: T-0457
title: Phase 2 · Account levels & assignment — confidence-weighted blend + suggest→confirm workflow
type: feature
state: triaged
created: 2026-06-22T21:41:39Z
updated: 2026-06-30T18:52:13Z
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
  - T-0496
  - T-0497
  - T-0495
  - T-0492
  - T-0488
  - T-0493
  - T-0486
  - T-0494
blocks: []
blocked_by:
  - T-0480
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 22
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

**2026-06-26 21:40 claude-code:** **Requirement (Austin, 26 Jun): a "fast-track lane" for high-potential new quotes.** When a new quote is flagged as potentially valuable, it must be routed to a **prioritised lane** — a queue with an owner + an SLA — so a promising lead gets immediate senior attention instead of sitting in the normal pile. This is the "untriaged-whale queue" already foreshadowed in the scorecard/design.

**Important re-scope from today's ML validation (see T-0481):** the original idea was to feed this lane from the *ML* white-whale model. Real-data validation shows that model **isn't viable** (whales too rare, and first-quote features don't beat quote size). So the fast-track lane should **not** wait on the ML — feed it from the signals we *do* have:
- the **scorecard** £-potential (interpretable, no training needed),
- the **quote value** itself (the strongest single predictor on the data),
- the customer's **segment + score** (once point-in-time per T-0456 / T-0473-4).

So this is a routing/workflow feature on top of the account-level model, not an ML dependency. Suggest it becomes its own ticket under T-0457's umbrella (lane definition, ownership, SLA, how a flagged quote enters/exits, and the threshold that triggers it — all tunable via T-0479). Flagging here so it's captured; happy to spin it into a dedicated ticket.

**2026-06-26 22:18 claude-code:** **Fast-track lane — designed, with an important correction (26 Jun).** Full design: `docs/p0018-sales-segmentation/P-0018-fast-track-lane.md` (commit 00dd91a0b). Built via a design panel + adversarial critic + an **empirical backtest on the real 6,898 first-quotes**.

**The headline changed during the work:** the lane was meant to be *scorecard-fed*, but the backtest shows that doesn't work — ranking the real quotes by the scorecard's "potential" catches **0 of 33 whales in the top 500** (worse than random; the scorecard floors small quotes and rewards high-volume-cheap orders over premium ones). **Plain quote value is the actual signal:** the 50 biggest new-customer quotes contain 7 of the 33 whales (14% precision); the top 500 catch about half. So the lane should trigger on **quote value** (plus the customer's web-score when we have it), *not* the scorecard.

**Honest scope:** even quote value is modest — this is "put senior eyes on the biggest new-customer quotes," not a magic whale-finder (the ML validation already proved hidden whales aren't detectable from a first quote). But it's cheap and real.

**What's solid (critic-confirmed):** the lifecycle / SLA / ownership / capacity / dedup scaffolding. **MVP:** a quote-value "biggest new quotes" star + filter on the existing `leads.php`, run in **shadow mode** for a few weeks to measure real precision before any SLA/senior-capacity commitment. Phased into the full lane (P2) and the account-level front door (P3). Depends on T-0480 (home row), T-0479 (params), and — for the web-score booster — the customer being scored+segmented point-in-time (T-0456 / T-0473-4).

Suggest this becomes its **own ticket** under this umbrella. Happy to spin it up.

**2026-06-27 02:31 claude-code:** **Requirement (Austin, reading the demo help with fresh eyes): the workflow needs a TRANSITION step after "confirmed".** Confirming a level is just the *decision* — it doesn't actually move the customer. There must be a distinct, explicit step that **takes the account out of where it sits today and moves it into the new level's cohort.** A confirmed level isn't "real" until this handover happens.

What the Transition step has to do:
- **Reassign ownership** to the new level's owner (the AM/exec/senior lined up at the "proposed" stage).
- **Handover briefing** — capture notes from whoever held the account before, so context isn't lost.
- **Stop the old handling** and **start the new level's playbook** — Strategic → bespoke plan, Account → AM cadence, Incubation → nurture track, System → automated only.
- Run it in the **transfer/review window** so moves are orderly (batched), not a mid-quarter scramble.

So the state machine is: suggested → proposed (owner lined up) → requirements met → **confirmed (decision)** → **transitioned (the move)**. The "transitioned" step is new and should be reflected in the workflow + data model (it's where `salesID`/owner actually changes and the playbook is triggered) — candidate for an acceptance criterion. This has been added to the demo's "How this works" help so stakeholders see the full path. Demo: `~/Documents/P-0018-population-demo.html` (hosted copy refreshed).

**2026-06-30 16:12 claude-code:** **Phase-2 design groundwork added (docs + fleshed tickets).** To make "what we do once segmented" coherent:

- **Coherent terminology** — `docs/p0018-sales-segmentation/P-0018-phase2-terminology.md`. One vocabulary: **Stewardship Level** = the bucket (System / Incubation / Account / Strategic); **Conversion Process** = how we handle one quote (System follow-up / Quick / In-depth / Lifetime); the **Conversion Path** states (Suggested → Proposed → Owner Assigned → Qualified → Confirmed) with **"in the bag %"**; **Conformance vs Effectiveness**; **Gold Nugget** = white-whale. Stops the "Account" layer-vs-value clash from Ben's board.
- **A cleaner process-flow** than Ben's (drops the Personal/Corporate/Events branching since Score+Segment already do that): `P-0018-phase2-process-flow.mmd` + a viewable `P-0018-phase2-flow.html`.
- **Fleshed the Phase-2 tickets** (acceptance criteria + design notes, all cross-linked): T-0496 per-cohort playbooks, T-0495 validation (AI suggests→human adjusts→human qualifies), T-0497 conversion-path visual + "in the bag %", T-0492 conformance, T-0488 KPIs, T-0493 dashboards, T-0485/T-0490/T-0491 the play/scripts/activities, T-0486 primary/secondary, T-0489/T-0494 Teresa review + nurture engine.

Ben's board questions remain the workshop calls (which levels, precise criteria per level, handover process, and Incubation ownership = execs vs an AM pool). Ben's PDF couldn't be opened in-tool; transcribed via rendered crops.

**2026-06-30 16:57 claude-code:** **HOW spec added — operating runbook.** `docs/p0018-sales-segmentation/P-0018-phase2-operating-runbook.md` covers, for every step of the flow (Intake → Score → Suggest → Validate → Propose → Assign → Qualify → Confirm → Steward → Convert → Review): **trigger · actor · system support · automated vs manual · output (next state) · conformance signal**, plus the **role relay** (AI · Outbound · Steward · Exec · AM · Senior AM · Manager) and the **handover** (step 6 — the one ownership change). It's parameterised: the workshop calls (levels/criteria, Incubation ownership = execs vs AM pool, the Steward, cadence intervals, the "mandatory steps" for conformance, thresholds/caps) are marked [WORKSHOP] and don't block the build. This is the spec the engine (this ticket) + T-0495/T-0497/T-0492/T-0488 follow. Next: step 2 (handover mechanics) then step 3 (MVP build).

**2026-06-30 17:10 claude-code:** **MVP engine + Account Elevation tracker built (sandbox, branch p0018-sales-segmentation-design, uncommitted).**

- **Table** `customer_account_levels` (migration `m260630_170000…`) — MVP slice of the design §6.1 (level, status, value axes, flags, in_bag_pct; effective_level computed in queries, not a generated column).
- **Recompute** `console/controllers/AccountLevelController.php` (`account-level/recompute` + `/stats`) — one home row per scored domain: value axes (hire-only, contractTotal), potential (score→£ curve), **suggested level via the ladder**, white-whale/big-fish, track new/existing, status=suggested. Result: **3,951 rows** — strategic 5 · account 151 · incubation 554 · system 3,241; 580 white-whales, 101 big-fish, 72 new.
- **Tracker view** `backend/controllers/AccountLevelsController.php` + `backend/views/account-levels/index.php` — read-only grid at **/account-levels**: suggested level, elevation stage, **"in the bag %"** bar, realised/potential/share-of-wallet, whale/big-fish flags, filters. (RBAC like the other pages: superadmin now; grant `/account-levels/*` for others.)

**Known MVP limitation (flagged):** realised £ is rolled by **email-domain**, so customers whose contract emails differ from the scored domain (the `.comOLD`/typo fragmentation we hit on the lane) show **£0 realised** and get mis-bucketed to incubation (e.g. barbican, royalalberthall, AEG). Fix = roll realised by **canonical customerID** — the T-0486/T-0480 identity work; that's the right next increment before this is trustworthy. Also still MVP: satellite rows, the nightly cron, and the qualify checklist (in_bag stays 0 until T-0495/T-0496 land).

**2026-06-30 17:23 claude-code:** **Fixed: account-level engine now keys/rolls by customerID, not email-domain** (Austin's call — the domain link was the gap). `account-level/recompute` now rolls realised hire by **canonical customerID** (merge-followed via `mergedIntoCustomerID`) and keys each account row by that customerID; email-domain is used only for the per-domain web-score lookup.

Result: **19,979 canonical accounts** (Strategic 13 · Account 191 · Incubation 790 · System 18,985; 822 white-whales). Validation — **Alexandra Palace** (cust 5340) now reads **£21,179 realised / £32k LTV → Account**, was £0 → Incubation under domain matching. (A few referral/dry-hire venues e.g. Barbican still show low realised — genuine: their clients hire from us, so high-potential + low-share = correct white-whale, not a bug.)

Still open: the score table itself is domain-keyed (inherent — a web-lookup is per-domain), and **truly-duplicate unmerged records** need the identity graph (T-0486/T-0480). MVP recompute is a full rebuild (truncate) — a live recompute must preserve `confirmed_level`. Saved as standing guidance: use customerID as the backbone.

**2026-06-30 18:52 claude-code:** **2026-06-30 — clarified "why a high-score account can sit in Incubation" + made the ladder legible** (sandbox, uncommitted; see TS-004).

Austin asked why some high-score / high-potential accounts are Incubation while some lower-score ones are Account/Strategic. Answer: **the level is earned by REALISED money, not the score** — exactly as this ticket's ladder intends. Strategic = realised ≥ £40k/12m + repeat + signal; Account = realised ≥ £8k/12m **or lifetime (LTV) ≥ £20k**; Incubation = high potential but low realised (grow it); System = low both; new caps at Incubation. So a 98A with £89k potential but £0 realised (Montgomery, Barbican) is *correctly* Incubation — a Gold Nugget to grow — and we don't promote on potential alone.

The confusion was a **display** gap: the worklist only showed Realised/12m, hiding the LTV rule (frieze, opusagency: £0 in the last 12m but ≥£20k lifetime → Account). Fix: added a **Realised LTV** column + a plain-English **"why this level"** explainer to `/account-levels` (index + detail). No model change — just surfacing the basis the engine already computes.
