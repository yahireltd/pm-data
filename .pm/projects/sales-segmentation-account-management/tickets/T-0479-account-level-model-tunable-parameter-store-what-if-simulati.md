---
id: T-0479
title: "Account-level model: tunable parameter store + what-if simulation tool"
type: feature
state: triaged
created: 2026-06-26T14:49:58Z
updated: 2026-06-30T18:52:22Z
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
  - "Every model parameter lives in a named, versioned parameter set, editable WITHOUT a code deploy: the score->£ potential-curve anchors, the segment fit multipliers, the alpha/evidence-weight function (orders-to-full-trust, recency half-life), the initial-quote weight (beta), the £ band cuts for the 4 levels, the white-whale share-of-wallet threshold, and the Strategic per-senior-AM capacity cap."
  - "The parameter set includes PER-SEGMENT PROFILES (one per industry/company-type from ADR-006/007): repeat_likelihood prior, potential_multiplier / segment-aware curve, default_conversion_process, optional band-threshold overrides, and qualification_set — so a user can tune one segment (e.g. 'venues' vs 'caterers') and watch that cohort re-band."
  - A what-if screen recomputes proposed account levels for any chosen parameter set IN MEMORY (no writes) over BOTH (a) the existing scored customer base + ya_contracts and (b) the recent-new-quotes cohort.
  - "The screen shows: distribution by level (counts + £), a Value x Potential scatter, and a DIFF vs the live parameter set ('N customers move X->Y'), with drill-down to individual customers; segment is a filter/colour dimension."
  - A parameter set can be saved, compared and promoted to live; each customer_account_levels row stamps the compute_version (param set) that produced it, so assignments are reproducible.
out_of_scope: []
code_anchors: []
relates:
  - T-0457
  - T-0456
blocks: []
blocked_by:
  - T-0480
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 7
---

## What this is

The **tuning + simulation layer** for the account-level model ([[T-0457]]). Every model parameter lives in a named, versioned **parameter set**, and a **no-write what-if screen** recomputes proposed levels over the real base in memory so you can SEE how the account-type assignments shift before committing a set to live. (Direct ask: "easily tune model parameters so we can see how the proposed account types change with different parameters.")

Design: `~/Documents/P-0018-account-level-blend-addendum.md` §D (parameter store) + §E (simulator). Decisions + calibration: **TS-003**. Hard prerequisite: [[T-0480]] (`email_domain` — the fact-frame rollup needs it; without it the sim is an unindexed scan, not "runnable today").

## Architecture

- **Pure engine** `common/components/AccountLevelEngine.php` — `computeRow(facts, params)`, **no DB, no side-effects**, returns the level + α/γ/realised_blend/realised_gate/potential/gap/status_stage/conversion_process/band_reason. The nightly recompute and the simulator call the **same** function — so the what-if's band logic is byte-identical to production. (Hysteresis / dormancy / committed_fwd demote-exemption stay in the nightly job — they need prior persisted state — and are approximated + labelled in the sim.)
- **Parameter store** — new `account_level_param_sets` table: one JSON bundle per set, `status ∈ draft/candidate/live/archived`, **exactly one live** (enforced transactionally in `actionPromote`), **immutable once promoted** (clone-to-edit), **schema-validated on save** (monotonic score anchors, `floor ≤ cut`, every knob range-checked). `customer_account_levels.compute_version` stamps which set produced each row.
- **Cached fact-frame** — `account_level_facts`: the param-INDEPENDENT raw windows + per-order ages + **frozen home-row identity**, built once so re-scoring the whole base per tweak is in-memory PHP.

## The screen — `AccountLevelsController` (sibling of `SalesScoresController`, manager-RBAC)
- **LEFT** parameter editor: constrained **monotonic curve editor** (server-validated so a non-dev can't draw a decreasing curve), the 4-row α maturity matrix, sliders/inputs for k/α_max/H/T0/β/quote_cap/band cuts/realised floors/graduation/value floors/lapsed-review/committed-fwd/white-whale/capacity/hysteresis/big-fish. Load-saved-set + compare-to-live.
- **TOP** distribution cards (count + Σexpected + Σrealised + Δ-vs-live); Strategic shows *suggested* AND *confirmable-under-cap* (joins live senior-AM headcount).
- **CENTRE** Value×Potential scatter (X potential, Y realised_blend, colour level, size expected); band-cut + floor reference lines move live; pending-quote overlay for mature-account pipeline.
- **DIFF vs live — TWO matrices:** suggested→suggested move count ("N move X→Y") + a separate confirmed-override panel (confirmed rows excluded from the raw count, no double-count).
- **DRILL-DOWN** per domain with α (+ binding term), realised_blend/gate, potential, quote, expected, gap, status_stage, conversion_process, live-vs-sim level, plain-English band_reason → full basis_json.
- **FOOTER** save-as-draft (new version) / promote-to-live (transactional, optional immediate recompute) behind a confirm modal re-showing the diff. Required honesty banners: frame-age (blocks promote if the frame predates the last live recompute) + "expected_value is steering value, not a forecast".

## References
Consumes [[T-0457]] (level logic) and [[T-0456]] (scores). Blocked by [[T-0480]].

## Conversation

**2026-06-26 20:43 claude-code:** **Update (26 Jun)** — the interactive demo, which is the "what-if simulation tool" this ticket describes, now has a **Defend | Grow** toggle. *Defend* ranks customers by what they spend with us today (the retention view); *Grow* ranks the same customers by their **potential** — where the next growth is — with accounts we've already largely won stepping aside so they don't crowd the list.

**Honesty note that belongs squarely on this ticket:** every threshold and weight in the demo right now is a **placeholder**, and the "potential" figure leans on an **uncalibrated** web-score, so the pound values it shows ("£X to win") are **indicative, not final**. Pinning these parameters down — and giving them a proper tunable store with someone signing them off — is the core of this ticket. The demo is the argument for *why* T-0479 is needed.

Demo + a design note (`docs/p0018-sales-segmentation/P-0018-defend-grow-lens.md`) are committed on branch `p0018-sales-segmentation-design` (commit 9c45b3ce1). The live 849-customer version was shared with Ben for feedback. The 849-customer population HTML and the source CSVs stay out of git (real customer data).

**2026-06-30 18:52 claude-code:** **2026-06-30 — bucket-split study to aim the what-if tool** (data from the live recompute; see TS-004).

Ran a distribution + boundary-sensitivity pass on the 19,979 canonical accounts to see where the current cuts actually bite:

- **The split is 95% System:** Strategic 13 · Account 191 · Incubation 790 · System 18,985. Only ~1,000 accounts get any human attention — so the real tuning question is the size/order of that ~1,000, not System-vs-rest.
- **The single most sensitive knob is the Incubation potential floor (£15k):** **508 accounts** sit at £10–15k just under it → dropping the cut to £10k roughly **doubles** Incubation (790 → ~1,300). This one threshold basically decides "how big is the book execs nurture?" → it should be set from **capacity** (how many accounts execs can realistically work), not a round number.
- **Incubation is almost entirely white-whales** (high tier-A score, near-zero realised, avg £200–£800/yr). So *inside* Incubation, chase-priority matters more than the boundary: only 10 at ≥£75k potential, 113 at £30–75k, 417 at £15–30k → the what-if should also offer a ranked/"park" sub-state, not a flat 790.
- **Account/Strategic are robust:** only ~158 accounts have ≥£8k realised/12m at all; just 9 near the Strategic floor. Real money is genuinely concentrated, so those bands are stable; the override tool handles the edge cases.

Recommendation for this ticket's what-if screen: let the user slide the four cuts and watch the bucket counts move live, with the **Incubation floor expressible as a target headcount** (set the cut at the Nth-percentile of potential). The MVP engine already stamps a basis and is customerID-keyed, so the sim can reuse it.
