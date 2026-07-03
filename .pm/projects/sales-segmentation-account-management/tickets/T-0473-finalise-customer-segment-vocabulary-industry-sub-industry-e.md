---
id: T-0473
title: Finalise customer segment vocabulary (industry+sub-industry, event-type+sub-type) + validate vs the base
type: feature
state: triaged
created: 2026-06-23T14:09:30Z
updated: 2026-07-03T03:18:04Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-011
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: web
  contact: austin@yahire.com
assignee: null
acceptance_criteria:
  - A richer controlled vocabulary is agreed for D2 industry + SUB-industry and D3 event-type + SUB-type (the existing 18 company_types and 11 event_types are too thin) — each grouped by class, SIC-mapped where relevant, per the ADR-004 model + ADR-003 governance.
  - "Validated against the live base: >= ~90% of active customers can be classified into a real industry (£value + volume known per segment); 'Unknown—to classify' is a tracked queue, not a dumping ground."
  - "Pressure-tested: each segment + value routes sensibly through Ben's Scenarios 1–5."
  - The agreed vocabulary is recorded as an accepted ADR (superseding the v0 strawman ADR-004) and handed to the build ticket as the enum the scrape writes into.
out_of_scope: []
code_anchors: []
relates:
  - T-0457
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - segmentation
  - research
  - for-austin
attention: null
version: 3
---

## What this is

Reinvent the thin existing vocabularies into a proper, complete segmentation vocabulary with **sub-types** — the gap that stops us saying "a Caterer who mainly does Weddings."

## Where we are (data-confirmed, 23 Jun)

- `ya_company_type` = a decent 18-value industry skeleton but **~96% unpopulated** and **flat (no sub-industry)**.
- `event_type` = **~99.8% empty**, 11 weak values — unusable; D3 is built fresh.
- Value concentrates in trade/venue/agency customers (Caterer ≈£21k/head, Exhibition organiser ≈£35k/head, Events Agency £2.7M/179) — so getting their segmentation right matters most.

## The work

Finalise D1–D4 (see [[ADR-004]]): expand industry into industry→sub-industry, build event-type→sub-type, keep the events-relevant flag + labels. Validate coverage + value against the base. Pressure-test vs Ben's Scenarios. Ratify as an ADR. Feeds the build ticket + the team brainstorm.

## Conversation

**2026-07-03 03:18 claude-code:** Status note (2026-07-03): this ticket's ratification condition is now met — **ADR-007 (taxonomy v5) was accepted by Ben + Austin** during the decision-ledger reconciliation, and ADR-006/ADR-009 with it. The vocabulary itself lives in P-0018-taxonomy-v5.md, is populated across 8,645 scored rows, and validation against the full base was done in TS-002 (528) + extended to the 8.6k sweep. What remains before closing: the scenario pressure-test AC references "Ben's Scenarios 1–5" which need renumbering at source (the PDF has two Scenario 5s and no 3 — flagged for the M-009 workshop), and the vocab ENUM handover to T-0474 (vocab tables + review queue) is still open on T-0474's side. Suggest: after M-009, close this ticket as delivered-by-ADR-007 and carry the residue on T-0474.
