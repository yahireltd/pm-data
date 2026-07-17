---
id: T-0575
title: Email templates — template manager (core + personal templates)
type: feature
state: triaged
created: 2026-07-15T07:21:36Z
updated: 2026-07-17T06:34:10Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: Nathan phase-1 prep doc
assignee: null
acceptance_criteria:
  - A template manager on the QB with core templates + optional per-salesperson personal templates (add/edit/remove).
  - Templates surfaced via dropdowns from the QB (and profile / other useful places).
  - Templates support pulled-through links/items, not just static uploads.
  - Inbound's email needs (what they send) captured before finalising the core template set.
  - AI-assisted emails scoped as an explicit later phase.
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
  - phase-1
  - email-templates
  - quote-builder
attention: null
version: 2
---

## Problem
The email-template system on the Quote Builder needs an overhaul: a template manager with core templates plus (optionally) personal templates per salesperson.

## Design notes (M-010)
- A **template manager** on the QB: **core templates** + optionally individuals create their own — not a dumb upload, since we often pull through links/items, so not for the unsavvy.
- Templates available on dropdowns from the **QB**, possibly the **profile**, and other convenient places.
- **Phase 1:** personalised templates per salesperson — what they can add/edit/remove.
- **Later:** AI-assisted emails.
- **Ownership:** Zsolt or Austin, or both — TBC.

## Actions
- Speak to **inbound** about what they send (accounts do some small ones, e.g. venue comms) — Frank / Helio / Sam.
- **Payment chasers** (Nathan) — noted these will be on different pages; already homed in **T-0418**.
