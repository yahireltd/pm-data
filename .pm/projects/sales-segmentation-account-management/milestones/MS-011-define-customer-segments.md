---
id: MS-011
slug: define-customer-segments
title: Define Customer Segments
project: sales-segmentation-account-management
state: planned
order: 5120
created: 2026-06-23T12:17:32Z
updated: 2026-06-23T13:52:23Z
acceptance_criteria:
  - A written segmentation model defines the dimensions — customer CLASS, INDUSTRY/sub-industry, EVENT-TYPES served, and operational LABELS — each with an agreed controlled vocabulary (a customer can carry several event-types + labels, one class + industry).
  - The model REUSES existing system structures where they exist (event_type lookup, customerType / ya_company_type, ya_customer_tiers, venues) and only CREATES new controlled vocabulary for industry/sub-industry (none exists today).
  - "Classification governance is defined: the enrichment scrape maps into the controlled vocabulary; no-fit cases route to a 'proposed' review queue (a human maps-or-adds, versioned) with a named owner — no silent new categories."
  - "Validated against the live base: >= ~90% of active customers classify into a real segment ('Unknown/Other' small), with volume + £value known per segment."
  - "Pressure-tested: each segment + value routes sensibly through Ben's Scenarios 1–5 — segmentation feeds score + account level, it does not duplicate them."
  - Agreed with the team and recorded as a decision; a review owner + cadence keep it accurate as customers are (re)scraped.
slip_records: []
phase_id: PH-002
version: 2
---

# Define Customer Segments

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
