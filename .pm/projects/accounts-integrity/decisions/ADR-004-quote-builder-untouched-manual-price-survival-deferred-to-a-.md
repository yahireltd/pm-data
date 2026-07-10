---
id: ADR-004
slug: quote-builder-untouched-manual-price-survival-deferred-to-a-
title: "Quote builder untouched: manual-price survival deferred to a gated review (T-0541)"
state: accepted
decided: 2026-07-10
decided_by:
  - Austin
project: accounts-integrity
supersedes: null
superseded_by: null
tickets:
  - T-0538
  - T-0541
version: 1
---

## Context
The quote builder's session writers wipe/overwrite manually-set prices on date changes (LTH threshold crossing) and qty changes (discount-matrix reprice) — a known contributor to pricing surprises. Austin initially chose "manual price survives + UI flag" for both cases, then reversed the same evening: he will not change his colleague's quote-builder functionality without a dedicated review.

## Decision
No T-0538 change touches SalesController session writers or quote-builder behaviour. T-0541 gates any future change and requires: (1) sign-off from Austin AND Zsolt, (2) quote-builder regression tests in place BEFORE implementation, (3) the priceSource provenance design (NULL = legacy = current behaviour) if survival rules are adopted. T-0541 also carries the package-pricing evidence (double-charged package+component, priced-vs-invisible package parents, duplicate item rows).

## Consequences
Manual prices continue to be wiped by date/qty changes until T-0541 concludes — staff re-apply fixes deliberately. The T-0538 branch is verifiably free of quote-builder changes (confirmed in review).