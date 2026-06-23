---
id: ADR-007
slug: segment-taxonomy-v5-validated-against-the-full-528-base-plac
title: Segment taxonomy v5 — validated against the full 528 base (+ place-of-worship type, peer/competitor label, venue-hire model, derived high-potential, closed sub-type vocab)
state: proposed
decided: 2026-06-23
decided_by: []
project: sales-segmentation-account-management
supersedes: ADR-006
superseded_by: null
tickets:
  - T-0473
  - T-0474
version: 1
---

## Context
ADR-006 set the 3-level model (Industry → company_type → sub_type) but it was never validated against the real base. We ran all 528 scored customers through the v5 web-lookup. Coverage was total: 528/528 classified, confidence high 393 / med 123 / low 12.

## Decision
Adopt **taxonomy v5** = ADR-006's three levels, plus the refinements the 528 run surfaced:

- **+ company_type "Place of worship / faith organisation"** (Religious industry). ~£0.3M of value was previously stranded in "Other" (HTB/Alpha, synagogues, mosques, churches that hire for festivals, courses, lifecycle events).
- **+ label "peer/competitor"** — 34 accounts that are themselves furniture/equipment/marquee-hire **suppliers**, not buyers (e.g. Funky Furniture Hire, GES, Sunbelt). Exclude from prospect lists.
- **+ field `venue_hire_model` ∈ {dry-hire, in-house, ""}.** For venues, dry-hire (clients must bring/hire their own furniture & catering) is the **highest-value Yahire signal**; in-house venues cater themselves (lower upside).
- **"high-potential" replaces "white-whale"** as a scrape label (judges size/prestige/event-scale only). **White-whale becomes a DERIVED flag** computed downstream = high-potential minus actual capture.
- **Closed sub-type vocab** for the high-value company types: Event organiser (Conference/summit · Exhibition/trade-show · Awards · Festival · Hackathon/community), Event agency (Experiential · Corporate · DMC · Production-led), Caterer (Wedding · Corporate · Private/social · Dietary-specialist · Private chef), Publisher ("B2B media + own events"), venues (Blank-canvas/immersive · Heritage · Multi-purpose).
- **Own-events rule retained:** primary-business-is-events → Events & Entertainment; otherwise the REAL sector + label `runs-own-events`.

## Value concentration (aggregate; row-level in local PII CSV)
- Events & Entertainment **53.7%** of lifetime value (262 accts), Media & Marketing **15.7%**, Hospitality **6.5%** → top-3 ≈ **76%**.
- 84 accounts (9.4% of value) are NOT events-relevant → deprioritise. 34 peer/competitor → exclude.

## Consequences
- **T-0473** (finalise customer segment vocabulary) is satisfied pending Ben/team confirmation.
- **T-0474** (one-pass enrichment scrape) builds the vocab tables + review queue to exactly these enums.
- State stays **proposed** until the team confirms — taxonomy ownership is Ben's per the Phase-2 plan; this is the data-grounded strawman to react to.
- Method + run mechanics are in TS-002; scoring conclusions in ADR-008.