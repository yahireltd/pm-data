---
id: ADR-005
slug: customer-segment-vocabulary-v0-2-fuller-class-industry-event
title: Customer segment vocabulary v0.2 — fuller class/industry/event-type tables + coverage-by-construction
state: rejected
decided: 2026-06-23
decided_by:
  - Austin
project: sales-segmentation-account-management
supersedes: ADR-004
superseded_by: null
tickets:
  - T-0473
  - T-0457
  - T-0456
version: 2
---

## Context

Supersedes the v0 strawman ([[ADR-004]]). Director feedback: "looks like it doesn't cover everything." This widens the named vocabulary AND makes the completeness argument explicit. Full tables live in the Brainstorm Pack (`~/Documents/Sales-Segmentation-Brainstorm-Pack.md`). Model + governance unchanged ([[ADR-001]], [[ADR-003]]).

## Completeness by construction

Every customer is **exactly one of 5 classes**, and every dimension carries an **"Other → review queue"** — so no customer is ever uncovered. The named lists below are the common ~95% (validated against the live base for coverage); the long tail flows to the governed review queue and is named over time. Completeness is structural; the list is a living, governed set.

## D1 · Class (one of 5)
Personal/Private · Corporate end-user · Trade/Intermediary · Venue · Public/Education/Non-profit.

## D2 · Industry → sub-industry (one of)
- **Trade:** Caterer (56210) · Events/management agency (82301/2) · Wedding & celebration planner · Production & technical (90020) · Stylist/florist/décor · Event hire & sub-rental (77390) · Entertainment & talent · Photography/videography · Film/TV/media · Tour operator/travel.
- **Venue:** Hotel (55100) · Dedicated event/conference venue · Wedding venue (93290) · Cultural/heritage venue · Members'/private club · Sporting venue · Restaurant/bar/pub (private hire) · Outdoor/temporary.
- **Corporate end-user (by sector):** Finance/professional · Tech/media/telecoms · Retail/consumer brands · Property/construction · Auto/manufacturing · Pharma/healthcare · Energy/utilities · Other.
- **Public/Education/Non-profit:** University · School/college · Student union · Government/public sector (council/central/NHS/emergency) · Charity/non-profit · Religious/faith · Membership/association/union.
- **Personal:** Private individual (class only — no industry).
- **+ Other → review queue.**

## D3 · Event-type → sub-type (multi, one primary)
Weddings · Corporate (conference/exhibition/launch/gala/meeting/staff-party/networking/VIP) · Festivals & public (music/food/cultural-fair/seasonal-market/civic/carnival) · Sports (stadium-VIP/tournament/mass-participation) · Private & social (milestone/religious/at-home/funeral/christening) · Education (graduation-prom/open-day/freshers) · Charity/fundraising · Film/TV/photo · **+ Other → review queue.**

## D4 · Labels (multi)
events-relevant · repeat/one-off · venue-referral · white-whale · qualified · share-of-wallet-known · Yamembership-candidate · seasonal.

## Status
Proposed — for the team workshop to confirm/amend, then validate coverage (≥90%) + value against the base (T-0473), and feed the scrape enum (T-0474).
