---
id: TS-004
slug: phase-2-mvp-build-account-level-engine-qualify-gate-validati
title: Phase-2 MVP build — account-level engine, qualify gate & validation/override loop
project: sales-segmentation-account-management
created: 2026-06-30T18:50:25Z
updated: 2026-06-30T18:52:00Z
decisions:
  - Stewardship Level is earned by REALISED money, not by the score/potential. Strategic = realised ≥ £40k/12m + ≥2 transactions + a strategic signal; Account = realised ≥ £8k/12m OR lifetime (LTV) ≥ £20k; Incubation = high potential but low realised (the grow bucket); System = low both; a brand-new customer caps at Incubation until realised £ accrues. So a high-score / £0-realised account (e.g. Montgomery, Barbican) correctly sits in Incubation as a Gold Nugget to grow, while a lower-score account with proven spend earns Account. To make this legible we added a "Realised LTV" column and a plain-English "why this level" line to the views — the LTV rule is what explains £0-in-12m accounts (frieze, opusagency) still showing Account.
  - Human corrections live in a SEPARATE durable table (customer_account_corrections), never in the engine table — because the nightly recompute truncates and rebuilds customer_account_levels, so anything written there would be wiped. Level overrides apply at read-time via COALESCE(override, confirmed, suggested); segment/score corrections are overlaid back onto the score table by a console step (apply-corrections) so the next recompute respects human truth. Every correction keeps the original AI value beside the human value, with a reason and who/when — an append-only audit log that also exports as the re-score feed. Same separation principle as the qualify table.
  - Correction dropdowns use the scoring taxonomy itself (industry + company_type pulled live from customer_sales_scores, frequency-ordered) as dropdown + type-to-autocomplete, not free text — so corrections converge on the same canonical values scoring assigned. A genuinely new value is still allowed and surfaces for the T-0474 review queue. The "in the bag %" preview is computed client-side mirroring the server rule exactly (checkbox ticked OR text/date filled), so there is no second source of truth — the server recompute on save remains the authority.
actions:
  - text: |-
      Build the what-if threshold tool (slide the £ cuts, watch the buckets move live). Bucket-split study shows the split is 95% System (Strategic 13 · Account 191 · Incubation 790 · System 18,985), and the single most sensitive knob is the Incubation potential floor (£15k): 508 accounts sit at £10–15k just under it, so dropping it to £10k roughly doubles Incubation. Recommend the tool ties the Incubation floor to capacity (how many accounts execs can nurture), not a round number.</parameter>
      <parameter name="action_ticket">T-0479
  - text: |-
      Workshop with Ben: confirm the levels + precise criteria, the Incubation ownership model (sales execs vs a dedicated AM pool), and the Strategic per-senior-AM capacity cap — this is the handover/ownership mechanics (step 2 of the operating-runbook HOW). The engine + override tool are ready; these are the remaining business calls.</parameter>
      <parameter name="action_ticket">T-0457
  - text: |-
      Deeper identity for truly-duplicate UNMERGED records, so referral/dry-hire venues' realised £ isn't understated (some £0-realised Incubation rows are really Account once their history is attributed). This is the primary/secondary customer-info work.</parameter>
      <parameter name="action_ticket">T-0486
  - text: |-
      Resume the customer-scoring sweep — ~19k domains still unscored (the run paused mid-Tier-5 on the monthly spend limit; ~3,990 scored + restored to sandbox). Resume once the limit resets/raises.</parameter>
      <parameter name="action_ticket">T-0456
side_quests: []
problem: |-
  TS-003 designed the conversion layer (two-track grid + suggest→confirm). This session BUILT the MVP on the sandbox and surfaced the data that should drive the threshold/ownership workshop. Three things shipped (read-only, uncommitted per policy): (1) the account-level engine, keyed/rolled by canonical customerID; (2) the qualify gate that turns "in the bag %" into a real, live checklist; (3) the validation/override loop where a human adjusts the AI's suggestion with a reason and that feeds re-scoring. Also answered Austin's question about why high-score accounts can sit in Incubation while lower-score ones are Account, and quantified where the bucket split actually bites.</problem>
  <parameter name="success_criterion">A working /account-levels MVP: suggested level + value picture; a qualify checklist computing in-the-bag % (live as you tick, before save); and a validation/override loop (change level/segment/score with a reason, logged, feeding re-scoring) — all on sandbox, uncommitted. Plus a bucket-split distribution study to inform the T-0479 threshold workshop.
phase: build
version: 8
---

# TS-004: Phase-2 MVP build — account-level engine, qualify gate & validation/override loop

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
