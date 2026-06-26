---
id: T-0479
title: "Account-level model: tunable parameter store + what-if simulation tool"
type: feature
state: triaged
created: 2026-06-26T14:49:58Z
updated: 2026-06-26T14:50:45Z
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
  - A what-if screen recomputes proposed account levels for any chosen parameter set IN MEMORY (no writes) over BOTH (a) the existing scored customer base + ya_contracts and (b) the recent-new-quotes cohort.
  - "The screen shows: distribution by level (counts + £), a Value x Potential scatter, and a DIFF vs the live parameter set ('N customers move X->Y'), with drill-down to individual customers."
  - A parameter set can be saved, compared and promoted to live; each customer_account_levels row stamps the compute_version (param set) that produced it, so assignments are reproducible.
out_of_scope: []
code_anchors: []
relates:
  - T-0457
  - T-0456
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

The **tuning + simulation layer** for the account-level model ([[T-0457]]). Every model parameter lives in a named, versioned **parameter set**, and a **what-if screen** recomputes proposed levels over the real base in memory so you can SEE how the account-type assignments shift before committing a set to live. (Direct ask: "we need to be able to easily tune model parameters so we can see how the proposed account types change with different parameters.")

## Why

The level model has many judgement-call parameters (band cuts, the alpha evidence-weight curve, segment multipliers, the initial-quote weight beta, the white-whale threshold, the Strategic capacity cap). These are the workshop's to settle, and the only honest way to tune them is to see the effect on real customers — not argue in the abstract. The sandbox data (TS-003) already shows how sensitive the picture is (86% of active customers < £2k/yr; only ~22 at £40k+), so small threshold moves shift hundreds of customers.

## The plan

- **Parameter store** — an `account_level_params` table of named, versioned sets (one flagged live). Every tunable lives here; `customer_account_levels.compute_version` records which set produced a row.
- **In-memory recompute service** — runs the [[T-0457]] level logic for a given param set over (a) the existing scored base + `ya_contracts` and (b) the recent-new-quotes cohort, returning proposed levels WITHOUT persisting.
- **What-if screen** — extend the `SalesScoresController` / `sales-scores/index.php` pattern (sibling `AccountLevelsController`): distribution by level (counts + £), a Value×Potential scatter, a DIFF vs the live set, drill-down; save a set, compare, promote to live.

## References

Full design: `~/Documents/P-0018-account-level-design.md` §D/§E (parameter store + what-if tool); decisions + calibration in **TS-003**. Consumes [[T-0457]] (the level logic) and [[T-0456]] (scores). Refined design from the blend/tuning design pass will be appended here.