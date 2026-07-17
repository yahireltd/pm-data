---
id: T-0619
title: Personal-cohort "golden nugget" spotter + split segments into Personal vs Personal-with-company
type: feature
state: triaged
created: 2026-07-17T20:25:44Z
updated: 2026-07-17T20:25:44Z
project: sales-segmentation-account-management
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: chat
assignee: null
acceptance_criteria:
  - Introduce two segment values — 'Personal' and 'Personal with company' — and classify the webmail cohort into them by presence of a company name (ya_customers.companyName)
  - "Standalone read-only page over the personal cohort surfacing the DB attributes that reveal potential: quote value & count, first/last quote date, any realised-order history, company name, email provider, geography where available"
  - Ranking / filtering that surfaces nuggets first (e.g. high total quote value, repeat quoting, event-type company names)
  - "'Personal with company' rows are scored (company-name lookup -> discovered domain, source=personal-email) and their realised-vs-potential gap shown"
  - "'Personal' rows are shown but left unscored (labelled as such)"
  - The two new segments appear consistently in the all-customer research view (sibling ticket) and any segment breakdown
  - Read-only, standalone, runnable on master
out_of_scope:
  - ML / pattern-detection model build (forward spike; see T-0481)
  - Bulk enrichment back-fill of the whole personal cohort
  - Write-back to customer records beyond the score rows we already write
code_anchors:
  - path: customer_sales_scores
    note: segment/classification + source flag (source=personal-email for named-webmail)
  - path: ya_customers
    note: email_domain, companyName (company-name-set detection), mergedIntoCustomerID
  - path: quotes
    note: quote value/count for the personal cohort
  - path: runtime/p0018/score_pipeline.php
    note: extract-quoteonly pattern -> add a named-webmail extract mode (score by company name, store under discovered domain)
  - path: backend/controllers/SegmentResearchController.php
    note: reference read-only drillable pattern for the new personal-cohort controller/views
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - p0018
  - phase-2
  - segments
  - personal-cohort
  - research
attention: null
version: 1
---

## Problem
A lot of real corporate demand hides behind **personal (webmail) email addresses** — a caterer quoting from a gmail address, a company buyer on hotmail. Domain-based scoring can't see them, so they sit unscored inside a ~52k "personal" bucket. We want a **standalone view over the personal cohort**, surfacing the DB attributes, to **spot the golden nuggets** — high-potential customers the domain scorer misses.

## Context
- Two distinct personal sub-cohorts, and Austin wants them made **explicit segments** (not just a runtime flag):
  - **Personal** — webmail address, **no** company name set → treat as genuinely personal, leave unscored (but still shown).
  - **Personal with company** — webmail address **with** a company name set (~1,380 today) → corporate-behind-personal-email; **score** by company name (discover the real domain, store under it, flag `source: personal-email`).
- This is where Ben's **ML / pattern angle** is most valuable: find the diamonds by pattern (quote value, repeat quoting, event-type company names), not by domain.
- Read-only, standalone — mirrors the `/segment-research` pattern so it can go on master.

## Notes / forward
- The actual ML model is a **follow-up spike**, not this build. Relates to T-0481 (quote-intrinsic white-whale detection).