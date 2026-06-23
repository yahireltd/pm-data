---
id: T-0474
title: "Extend the enrichment scrape: one pass → class/industry/event-types/labels + score; vocab tables + review queue; back-fill"
type: feature
state: triaged
created: 2026-06-23T14:09:56Z
updated: 2026-06-23T14:09:56Z
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
  - The TS-001 web-lookup scrape is extended so ONE pass per domain emits the segment tags (class, industry+sub, event-types+sub, labels) AND the score together — not two pipelines.
  - "Classification is into the agreed controlled vocabulary (closed vocab); no-fit cases write 'proposed: <phrase>' to a review queue — no silent new categories (per ADR-003)."
  - New/extended tables hold the controlled vocab (industry+sub, event-type+sub) + a per-customer segment record + the review queue; reuse company_type / event_type / customerType / tiers / venues where sensible.
  - Back-fill the existing scored customers and classify the rest; surface on the customer/quote screens with confirm/override + correction-logging.
  - Re-runnable as a Yii console command (extends the TS-001 runbook), not a one-off agent run.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - segmentation
  - build
  - yasystem
  - for-austin
attention: null
version: 1
---

## What this is

The build that turns the agreed vocabulary (T-0473) into populated data. Per [[ADR-003]]: segment + score come from **one enrichment pass** (extend the TS-001 method), classifying each domain into the controlled vocabulary with a review-queue for no-fits.

## Depends on

- T-0473 (the agreed vocabulary = the enum to write into).
- TS-001 (the replicable web-lookup runbook to extend).
- T-0456 (scoring) — same pipeline.

## Scope note

Build lands in **Yasystem** (Ya-Hire-Management) under its own ticket/branch; this card tracks it from the project side. Event-types (D3) are populated by the scrape (web-lookup), since the internal `event_type` is ~99.8% empty.