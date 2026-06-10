---
id: ADR-010
slug: benzinga-earnings-purchased-99-mo-after-p1-verification-pass
title: Benzinga Earnings purchased ($99/mo) after P1 verification PASS; keep/cancel gated on small-cap retest
state: accepted
decided: 2026-06-10
decided_by:
  - Austin
  - Claude Fable 5
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
kind: dependency
version: 1
---

## Context
PIT consensus EPS was the audit's #1 ML unlock. Sourcing review (2026-06-10): IBES out of budget; Zacks ZEEH quote-only; Benzinga Earnings on the existing Polygon/Massive account at $99/mo with per-event frozen estimates to 2010. P1 trial verification PASSED: 26/28 estimate matches vs contemporaneous press (split-adjusted), 28/28 actuals, freeze semantics confirmed via last_updated pattern (docs/benzinga_p1_verification.md).

## Decision
Purchased. Full history backfilled (240,823 confirmed events, 9,032 tickers, 2011–2026 — on disk regardless of subscription). Keep/cancel decision gated on the small-cap PEAD retest (MS-014) before the next billing cycle.

## Sharp edges
Values retroactively split-adjusted at 2dp — use only scale-invariant quantities (surprise-percent, price-scaled SUE, ranks), never cents. Benzinga consensus panel diverges from Refinitiv ~3–5¢ on a minority of events. Coverage thin pre-2015. YHOO re-tickered to AABA. BBBY-style ticker reuse needs PIT care. Benzinga NEWS entitlement separately lapsed in the Polygon→Massive transition (historical news data cannot be refreshed on the current plan).