---
id: ADR-006
slug: de-conflate-industry-from-company-type-three-levels-industry
title: "De-conflate Industry from Company-type — three levels: Industry (sector) → Company type → Sub-type"
state: accepted
decided: 2026-07-03
decided_by:
  - Austin
  - Ben
project: sales-segmentation-account-management
supersedes: ADR-005
superseded_by: null
tickets:
  - T-0473
  - T-0474
  - T-0457
version: 2
---

## Context

Director feedback: we'd **conflated Industry with Company type**. The live DB field is literally `ya_company_type` (Caterer, Production company, Hotel…) — that's **company type**, not industry. The clincher: a **Production company** can be in **Events & Entertainment** (events production — high value to Yahire) OR **Media & Marketing/Film** (film/TV production — low value). Same company type, different **industry** → very different value. So Industry must sit *above* company type.

## Decision — three nested levels

1. **INDUSTRY (sector, top, NEW controlled vocab):** Events & Entertainment *(the high-value events industry)* · Hospitality · Sports & Leisure · Arts & Culture · Education · Healthcare · Government/Public Sector · Charity/Non-profit · Religious · Membership · Finance & Insurance · Professional Services · Technology · Media & Marketing · Retail & E-commerce · Manufacturing · Construction & Property · Transport & Logistics · Agriculture & Environment · Personal/Private · (Other → review queue). *Industry is the primary value lens.*
2. **COMPANY TYPE (mid) = the existing `ya_company_type`**, nested under an industry — Caterer, Event agency/organiser, Event production company, Wedding planner, Event-hire, Hotel, Restaurant/bar, Stadium, University, NHS trust, Bank, Film/TV production company, etc.
3. **SUB-TYPE (specialism):** Caterer → wedding / fine-dining / festival / contract; Event production → conference / exhibition / festival / activation; Production (Media) → film / TV / commercial.

Plus (unchanged): **Event-types** (multi, what events they serve) + **Labels**. `events_relevant` is now derived ≈ Industry ∈ {Events & Entertainment, Hospitality-venue, Sports, Arts} OR the company runs its own significant events.

## Consequences

- Supersedes the v0.2 vocabulary (ADR-005) and refines the data model (ADR-003): `ya_company_type` = level 2; we ADD a new **Industry** table (level 1) and a **Sub-type** (level 3).
- The scrape (T-0474) now emits Industry + Company type + Sub-type — and only the scrape (reading the site) can tell events-production from film-production, which is the whole point.
- Closed-vocab + review-queue governance (ADR-003) applies to all three levels. For team ratification at the brainstorm.