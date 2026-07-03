---
id: T-0474
title: "Extend the enrichment scrape: one pass → class/industry/event-types/labels + score; vocab tables + review queue; back-fill"
type: feature
state: triaged
created: 2026-06-23T14:09:56Z
updated: 2026-07-03T00:24:56Z
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
relates:
  - T-0495
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
version: 3
---

## What this is

The build that turns the agreed vocabulary (T-0473) into populated data. Per [[ADR-003]]: segment + score come from **one enrichment pass** (extend the TS-001 method), classifying each domain into the controlled vocabulary with a review-queue for no-fits.

## Depends on

- T-0473 (the agreed vocabulary = the enum to write into).
- TS-001 (the replicable web-lookup runbook to extend).
- T-0456 (scoring) — same pipeline.

## Scope note

Build lands in **Yasystem** (Ya-Hire-Management) under its own ticket/branch; this card tracks it from the project side. Event-types (D3) are populated by the scrape (web-lookup), since the internal `event_type` is ~99.8% empty.

## Conversation

**2026-07-03 00:24 claude-code:** Reconciliation (T-0474) — work exists on branch p0018-sales-segmentation-design (commit cede0ffb7, done via tech sessions/direct commits outside the claim flow), but this ticket is roughly half-built and its body overstates what landed.

BUILT (file:line):
- Single-table segment+score store: m260629_120000_extend_customer_sales_scores_segment.php adds fit/freq/scale + industry/company_type/sub_type/venue_hire_model/primary_event_type/labels/classification/confidence to customer_sales_scores — one pass writes both (loaded together by CustomerScoringController::actionLoadResults, console/controllers/CustomerScoringController.php:223-268).
- Re-runnable console pipeline: customer-scoring/load528 (:81), extract-tier blind worklists incl. quoteonly band (:141), load-results, stats (:284), export for sandbox-wipe survival (:315), segment-profile (:372). Anti-leakage: lifetime spend computed at load time, never given to the scorer.
- Human validation loop: customer_account_corrections (m260630_190000, append-only), AccountLevelsController::actionOverride (backend/controllers/AccountLevelsController.php:133-186) with revert, and account-level/apply-corrections (console/controllers/AccountLevelController.php:155) overlaying segment/score corrections back onto customer_sales_scores.

OUTSTANDING (the ticket's headline items):
- NO vocab tables (industry+sub, event-type+sub) — vocab lives only in P-0018-taxonomy-v5.md; AccountLevelsController.php:207 literally says "Later: switch to the T-0474 vocab tables once they exist."
- NO review queue and NO 'proposed: <phrase>' handling anywhere (ADR-003 requirement) — load-results accepts free text and truncates it; new categories can slip in silently.
- NO surfacing on the existing customer/quote screens — confirm/override lives only on the new /account-levels screens.
- Back-fill not durable: runs were against the nightly-wiped sandbox only; migrations not applied to live; the scrape step itself is an uncommitted subagent workflow ("p0018-score-tier"), so the extended TS-001 runbook isn't versioned.

Suggest rescoping: mark the pipeline/loop halves done, and split the remaining scope (vocab tables + review queue + customer/quote-screen surfacing + production back-fill) into explicit follow-ups so this card stops implying the vocab discipline exists. Note: taxonomy v5 is now formally ACCEPTED (ADR-007, ratified 2026-07-03), so the vocab-table enum can be built without waiting on further ratification.
