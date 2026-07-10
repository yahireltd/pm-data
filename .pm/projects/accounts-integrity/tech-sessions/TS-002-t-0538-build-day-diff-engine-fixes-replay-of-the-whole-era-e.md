---
id: TS-002
slug: t-0538-build-day-diff-engine-fixes-replay-of-the-whole-era-e
title: "T-0538 build day: diff-engine fixes, replay of the whole era, E2E, adversarial review, Xero posting proof"
project: accounts-integrity
created: 2026-07-10T21:45:26Z
updated: 2026-07-10T21:47:07Z
decisions:
  - "Root causes located to exact code: rounding residuals smeared into a line field Xero ignores (the broken-document maker); charge-cancellation credits sharing the identity of the charge they cancel (double-sized credit notes); VAT code fragility incl. no-VAT contracts ignored by amendment lines; a mistyped negative compensation double-negating to positive. Fix principles locked as ADR-002 (never block generation; labeled Xero-valid adjustment lines), ADR-003 (forward-only writer changes), ADR-005 (VAT follows charge type — Austin's correction withdrew a wrong product-based inference)."
actions:
  - text: "Proof chain, in order: unit tests against the REAL engine proven failing-then-passing on pre-fix code (39 tests); full-era replay 4,128 docs → 4,113 byte-stable, exactly 1 real-customer broken doc in the era (the £180 case), every exception explained (test contracts + go-live-week hand-fixes); 24-check end-to-end of the charge/compensation flows incl. the penny-rounding stress; three independent adversarial review agents — findings fixed same-day (Xero line-validity split, sandbox guard, sales-contract scope, an honest revert of duplicate audit logging built on a wrong premise); final proof posting 34 docs to the Demo Company — all 28 correct-shape accepted with totals matching to the penny, all 6 pre-fix-shape rejected by Xero with the predicted error."
side_quests:
  - "Discovered along the way and routed to tickets: internal test contracts leaking into live Xero (T-0542); quote-builder package-pricing defects + manual-price wipes (T-0541 evidence); Xero deep links broken under the new UI (fixed via XeroLinkHelper); demo-reset ritual additions (accounts 201/202, org shortcode param)."
problem: Documents were being generated with headers disagreeing with their lines (the adjuster smear), credit notes doubling on charge reversals, and VAT slips — each becoming a manual IT data fix. One working day to build the fixes, prove them against every historical document, and review the lot.
success_criterion: Every fix pinned by a test that fails on the old code; every historical document replayed with nothing healthy disturbed; the corrected document shapes accepted by Xero itself; an adversarial review with no surviving blockers.
phase: build
version: 2
---

# TS-002: T-0538 build day: diff-engine fixes, replay of the whole era, E2E, adversarial review, Xero posting proof

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
