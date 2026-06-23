---
id: ADR-003
slug: segmentation-data-model-corrected-extend-existing-company-ty
title: "Segmentation data model (corrected): extend existing company_type as the industry base; rebuild event-type/sub-type; closed-vocab review-queue governance"
state: rejected
decided: 2026-06-23
decided_by:
  - Austin
project: sales-segmentation-account-management
supersedes: ADR-002
superseded_by: null
tickets:
  - T-0457
  - T-0456
version: 2
---

## Context

Supersedes ADR-002, which assumed "no existing industry table." A live-data query (23 Jun) corrected that and quantified the gap.

**Already exists (reuse / extend):**
- **Industry → `ya_company_type`** IS an industry vocabulary (18 values: Caterer, Dry Venue, Non-Dry Venue, Events Agency, Wedding Planner, Hotel, Exhibition organiser, Exhibition Provider, Production company, Brand Agency, AV Company, Event hire company, Charity, Council, College/School/University, + meta "Other events industry", "Other non-events industry", "Undefined"). **But ~96% of customers are Undefined/NULL.**
- **Class → `customerType`** (1 Personal / 2 Corporate); **Level → `ya_customer_tiers`** + A/B/C score tier; **Venue → `venues`**; **Value → `ya_contracts.contractTotal`** (`ya_customers.contractsValue` is unmaintained = 0).

**The value evidence (why this matters):** of ~£29.9M contract value, **~£18.1M (60%) is Undefined/unset** (unclassified). Among the classified, a few hundred trade/venue customers carry it: Events Agency £2.7M (179 custs), Non-Dry Venue £2.2M (149), Caterer £1.7M (80, ≈£21k/head), Dry Venue £1.1M (63), Exhibition organiser £0.78M (22, ≈£35k/head). The long personal tail is tens of thousands of low-value one-offs — exactly Ben's "point sales at high-value, system-handle the rest" thesis.

**The real gap:** **`event_type`** exists but is **~99.8% empty** (42,928 of ~43,000 contracts unset) with a weak 11-value vocab — so event-type / sub-type ("Caterer → Weddings") **must be built**, not read.

## Decision

1. **Extend, don't replace, `ya_company_type`** as the D2 industry vocabulary — rationalise it (split true industry from the "events/non-events" meta flag), and **populate the unclassified majority** via the enrichment scrape.
2. **Build a proper event-type / sub-type layer (D3)** — a controlled vocabulary linking industry to what they do; replace the weak `event_type`; derive from quote history + scrape + capture-going-forward.
3. **Governance — closed vocabulary + review queue (no silent minting):** the scrape maps into the agreed vocab; no-fit → `proposed: "<phrase>"` to a review queue; the named owner maps-or-adds (versioned).

## Consequences

- Head-start from the existing list; work = rationalise + populate at scale + build the missing event/sub-type layer. Score + segment from one enrichment pass. Build lands in Yasystem. Confirm/override + correction-logging (T-0457) = the human side of the review queue. For team ratification at the brainstorm.
