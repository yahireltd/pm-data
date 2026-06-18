---
id: T-0418
title: Customers Going Ahead (CGA) — automate / streamline the check-list
type: feature
state: triaged
created: 2026-06-18T08:34:36Z
updated: 2026-06-18T08:34:36Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Nathan Bruce
  channel: email
  contact: nathan@yahire.com
assignee: null
acceptance_criteria:
  - The CGA list shows each contract going ahead with inline flags for the checklist items (no manual per-contract eyeballing).
  - Flags any contract with no mobile / contact number present.
  - Flags landline-only contacts for review, indicating whether it's a delivery/collection venue or premises.
  - Flags goods missing accessories or with incorrect accessory amounts (e.g. odd combos like 1 post + 2 ropes, or odd multiples of pack size).
  - Flags addresses containing a floor number (to confirm ground floor) and surfaces specific delivery messages for review.
  - Flags quoted linen that still needs booking/confirming.
  - Flags 'Add cap' not entered correctly. (Clarify 'Add cap' definition.)
  - Surfaces quantities/accessories that look off for human confirmation.
  - BDC orders are retained and added to the Google Doc where needed. (Clarify 'BDC' and whether the Google Doc dependency stays or moves in-system.)
  - Payment chasers are incorporated into the same list rather than handled separately.
  - Each item distinguishes auto-validated checks from those needing a human action. (Define which are which.)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sales
  - contracts
  - customer-service
attention: null
version: 1
---

## Problem

The "Customers Going Ahead" (CGA) check is a manual, time-consuming review of contracts going ahead — a value-saving (not value-adding) activity. Nathan: "a big time killer," too many to use as an upsell opportunity. Ben: improve handling time with a designated/revised list that surfaces the checklist automatically; could free up a few hours a week.

## Checks to surface / auto-flag (the checklist)

- **Contact number** present — flag if no mobile / any contact number
- **Landline handling** — if landline, check specific numbers are added; else determine if it's a delivery/collection venue or premises
- **Accessories** — goods have all accessories at correct amounts; flag odd combos (e.g. 1 post + 2 ropes, odd multiple of pack size) for client confirmation
- **Address & messages** make sense; if a floor number is in the address, confirm ground floor with the client
- **Linen** — if quoted, flag to book/confirm the order
- **Add cap** entered correctly
- **Quantities/accessories** look OK
- **BDC orders** — keep in place; add to the Google Doc if necessary

## Enhancements (Ben)

- A revised CGA list view that flags these items inline (no manual eyeballing of each contract)
- Fold in related handling — e.g. payment chasers — rather than handling separately
- Likely owned by Customer Service (value-saving, not upsell)

## Open questions / to clarify

- Internal jargon to pin down: "BDC orders", "Add cap", and whether the Google Doc dependency stays or moves in-system
- Which checks can be auto-validated vs merely flagged for a human
- Ownership — Customer Service vs Sales
- Upsell explicitly out of scope for now

## Note

2024 thread — confirm it's still wanted before sizing.