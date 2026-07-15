---
id: T-0551
title: "Quote Builder: automatic transport-cost discounts & surcharges (by company profile and venue)"
type: feature
state: triaged
created: 2026-07-13T10:38:26Z
updated: 2026-07-15T10:22:01Z
project: sales-segmentation-account-management
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: email (forwarded request)
assignee: null
acceptance_criteria:
  - On the Quote Builder, the transport cost can be adjusted automatically (discount OR surcharge) instead of the salesperson editing it manually every time.
  - A transport adjustment can be configured at the company / customer-profile level and is applied automatically to that account's quotes.
  - A transport adjustment can be configured at the venue level in the Venue Database, and is applied automatically when a venue is assigned to the quote (e.g. an ExCel surcharge).
  - Both discounts and surcharges are supported (e.g. ExCel transport +x%, or a surcharge to offset a venue's commission demand).
  - When both a company-level and a venue-level adjustment apply, the system resolves them by an agreed rule (stack vs precedence — TBC) and shows the salesperson what was applied and why.
  - The applied adjustment is transparent on the quote (shown in the transport breakdown / a note), not a silently changed number.
  - Quotes for customers/venues with no configured adjustment behave exactly as today.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/views/sales/sale-quote-builder.php
    note: Quote Builder UI — where transport cost is shown/edited; surface the auto adjustment + explanation here
  - path: ya-hire/backend/controllers/SalesController.php
    note: Quote Builder actions / transport cost handling — apply the company + venue adjustment logic
  - path: ya-hire/backend/controllers/VenueDbController.php
    note: Venue Database — where a per-venue transport adjustment would be configured and read from
relates:
  - T-0470
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quote-builder
  - transport-cost
  - pricing
  - venue-database
  - account-management
  - sales
attention: null
version: 4
milestone: MS-001
---

## Problem
Transport-cost **discounts** (and now **surcharges**) are applied by hand on the Quote Builder today — the salesperson has to remember and manually adjust every time (examples cited: **Cater London**, **The Brewery**). This is error-prone and doesn't scale. The ask is for the **system to apply the adjustment automatically**.

## Context (from the request)
Drive the adjustment from two places:
1. **Company profile** — an account-level transport adjustment that applies to that customer's quotes.
2. **Venue Database** — a per-venue transport adjustment, applied automatically **when a venue is assigned** to the quote.

Both **discounts and surcharges** are wanted, e.g.:
- **ExCel** — perhaps transport costs slightly higher than others (TBC).
- **Venue commission** — some venues now push for commission on transport; a surcharge on transport for that venue could counteract it (TBC).

## Related / future (not this ticket)
The requester notes the venue adjustment "only applies if the venue is assigned", and links this to a future idea of the **Venue Database connecting to the cart** (customer enters a postcode → *"Hi, is your event taking place at ExCel?"*). That auto-detection is a separate, later piece — this ticket is about applying the adjustment on the **Quote Builder** once a company/venue is known.

## Open questions to confirm before building
1. **Adjustment type** — percentage, fixed £, or both? Any cap/floor?
2. **Stacking rule** — when a company-level AND a venue-level adjustment both apply, do they stack, or does one take precedence? Which?
3. **Where configured & by whom** — company adjustment on the customer profile; venue adjustment in the Venue Database admin — confirm the exact screens and which roles can set them.
4. **Customer visibility** — is a surcharge shown to the customer as a line, or absorbed into the transport total?
5. **Scope** — Quote Builder only for v1, or also the website cart? (Cart ties into the future venue→postcode piece above.)
6. **Reporting** — do we need to track how much discount/surcharge is applied per account/venue (for margin visibility)?

## Notes
- Likely needs a short design/scoping pass first (data model for the adjustments, the stacking rule, and the two config UIs) before build — acceptance criteria below are a first draft; confirm the open questions with the requester (Definition of Ready).
- Keep existing behaviour unchanged for accounts/venues with nothing configured.

## Conversation

**2026-07-15 10:18 claude-code:** **From the Sales Phase 1 prep doc (main-pages review):** Nathan raised **customer discounts on delivery prices** — i.e. a customer-level discount applied to delivery/transport charges.

This is the **customer-level angle of the same delivery-pricing-discount feature** this ticket already covers (company-profile + venue transport adjustments). Worth designing together, with a single delivery-charge adjustment that can be sourced from: **venue**, **company profile**, and/or **specific customer**. Flag to pick up when this ticket is scoped.
