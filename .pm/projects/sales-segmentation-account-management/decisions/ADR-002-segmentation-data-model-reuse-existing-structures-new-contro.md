---
id: ADR-002
slug: segmentation-data-model-reuse-existing-structures-new-contro
title: "Segmentation data model: reuse existing structures; new controlled-vocabulary industry with review-queue governance"
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

The enrichment scrape needs to know *where* a customer's industry/type goes — into an existing structure or a new one. A schema review of the live Yasystem DB (Ya-Hire-Management) settled what already exists.

**What already exists (reuse, don't duplicate):**
- `event_type` lookup + `quotes.eventType` / `ya_contracts.eventType` → **D3 event-types** are already structured, and each customer's real event-mix is derivable from quote/contract history (no scrape needed for existing customers' event-types).
- `ya_customers.customerType` (1 Personal / 2 Corporate) + `ya_company_type` lookup → **D1 class** (extend, don't replace).
- `ya_customer_tiers` + the A/B/C `customer_sales_scores.tier` → the **account-level / value** layer.
- `venues` (FK to customer) → the **Venue** segment.

**What does NOT exist:** any industry / sub-industry / sector table. The only industry signal today is the free-text `customer_sales_scores.business` field — uncontrolled, ~530 near-unique strings.

## Decision

1. **Reuse** the existing structures above for class, event-type, level and venue.
2. **Create a new controlled vocabulary** for **industry / sub-industry (D2)** — it has no home today, so we build it clean.
3. **Governance — closed vocabulary + review queue (no silent minting):** the scrape classifies each customer into the agreed industry vocabulary; when nothing fits well it writes `proposed: "<phrase>"` to a **review queue**, and the named segment owner maps it to an existing node or formally adds a new one (versioned). The scraper never invents a new industry on its own.

## Consequences

- Clean aggregation + reporting from day one; the taxonomy still evolves from real data, but under review — avoiding the 200-near-duplicate-"caterer" mess the free-text field already shows.
- The build (T-0456 scrape extension + the new industry table/vocabulary + review queue) lands in Yasystem under its own ticket; the score and segment come from one enrichment pass.
- Confirm/override + correction-logging (already in T-0457) double as the human side of the review queue.
- For team ratification at the segmentation brainstorm.