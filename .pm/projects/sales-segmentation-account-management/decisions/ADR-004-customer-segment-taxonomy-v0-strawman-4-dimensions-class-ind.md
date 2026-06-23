---
id: ADR-004
slug: customer-segment-taxonomy-v0-strawman-4-dimensions-class-ind
title: "Customer segment taxonomy v0 (strawman): 4 dimensions — class / industry / event-types / labels"
state: proposed
decided: 2026-06-23
decided_by:
  - Austin
project: sales-segmentation-account-management
supersedes: null
superseded_by: null
tickets:
  - T-0457
  - T-0456
version: 1
---

## Context

The strawman to take into the team brainstorm — built from the existing `ya_company_type` vocabulary, the live value data (ADR-003), external frameworks (UK SIC 2007; standard event-industry + B2B segmentation models), and Ben's PDF. A customer = tags on **4 dimensions** (one class + one industry; many event-types + labels), composing into score + account level per [[ADR-001]]. Governance per [[ADR-003]] (closed vocab + review queue).

## Strawman

**D1 · CLASS** (one-of): Personal/Private · Corporate end-user · Trade/Intermediary · Venue. *(Derive from a cleaned company_type→class map; today's `customerType` only splits Personal/Corporate.)*

**D2 · INDUSTRY** (one-of) — rationalise the existing 18 `company_type` values, grouped by class, SIC-mapped:
- Trade: Caterer (56210) · Events/Conference/Exhibition agency (82301/82302) · Production/AV/Event-hire (77390/90020) · Wedding planner · Brand/experiential agency (82990)
- Venue: Dry venue · Non-dry venue (93290/68209) · Hotel (55100)
- End-user: College/School/University (85) · Council/public (84) · Charity (88) · Corporate-other (by sector)
- DROP meta values "Other events/non-events industry" + "Undefined" as industries → replace with an **events-relevant flag** (D4) + an explicit **"Unknown — to classify"** queue.

**D3 · EVENT-TYPES / SUB-TYPE** (multi, with a primary) — NEW layer, replaces the weak `event_type`; **populated by the enrichment scrape** (web-lookup): Wedding (luxury/micro) · Corporate (conference/trade-show, launch/activation, gala/awards, meeting, staff-party) · Public/Festival (music, food/cultural fair, seasonal market) · Sports (stadium VIP, tournament, mass-participation) · Private/Social (milestone, at-home) · Exhibition.

**D4 · LABELS** (multi): events-relevant · repeat vs one-off · venue-referral · white-whale · qualified · share-of-wallet-known · Yamembership-candidate.

## Worked example
"A caterer who mainly does weddings" = Class **Trade** · Industry **Caterer** (56210) · Event-mix **70% Wedding** / 20% Corporate-gala / 10% Private · Labels repeat, events-relevant · Score **B** · Value ≈£21k/yr → Account level **Account-managed**.

## Status
Proposed — strawman for the team brainstorm to confirm/amend, then validate against the base (≥90% classify; volume+£value per segment) and pressure-test vs Ben's Scenarios 1–5. Sources: UK SIC 2007 (ONS / Companies House); event-industry + B2B segmentation references.