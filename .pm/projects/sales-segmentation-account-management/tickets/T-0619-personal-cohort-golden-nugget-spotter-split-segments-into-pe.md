---
id: T-0619
title: Personal-cohort "golden nugget" spotter + split segments into Personal vs Personal-with-company
type: feature
state: triaged
created: 2026-07-17T20:25:44Z
updated: 2026-07-17T22:40:21Z
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
version: 3
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

## Conversation

**2026-07-17 22:27 claude-code:** **Data note discovered while scoring the cohort (affects this ticket's build):**

Personal-email scored rows are keyed by the *discovered corporate domain*, but the customer's actual hire is booked under their **personal email address**. So realised £ must NOT be read off the scored row's domain — it must be computed via the **customer / email link** (match the customer's webmail email against `ya_contracts.email`). Measuring by domain understates their realised spend to ~£0.

Evidence: of 5,923 named-webmail customers, **975 already have realised hire (~£737k)** booked under personal emails — invisible to the original domain-keyed sweep. So the "Personal with company" segment contains a meaningful chunk of *realised* corporate customers, not just leads; the golden-nugget view should surface realised £ (via the customer link) alongside the score/potential.

Add to acceptance criteria: realised £ for personal-email customers is computed via the customer/email link, not the scored-row domain.

**2026-07-17 22:40 claude-code:** **Personal (no-company) cohort — exploration + "how to spot repeat within" (analysed 2026-07-17).**

Scope: the ~50,559 personal records (webmail, no company name). Can't be scored (no company to look up), but there's real money hidden in here — the tool is about exploration + repeat detection, not scoring.

What the data shows:
- **8,347** have realised hire (~16.5%); **10,402** delivered orders total.
- **1,216 repeat buyers (>=2 delivered orders) worth £1.33M realised LTV** — an invisible loyalty base sitting inside "unscored personal".
- **446 of those repeaters are dormant** (last order >2y ago) = a ready-made **win-back list**; 770 still active. (This is Ben's "not trading since" sales-intel, and it's separate from any score.)
- Top repeaters are individuals ordering 8–20 times (e.g. one customer 20 orders, another 12 orders / £19k).

**How to spot repeat — tested four identity keys, they agree to within ~1%:** customerID 1,216 · email 1,207 · phone 1,206 · name 1,205. So the **system's customerID identity already captures repeat reliably** here — no fuzzy-matching engine needed. Fragmentation (same person split across >1 customer record) is small: only **127 phone numbers** and **19 emails** span multiple customerIDs.

Recommended method for the tool:
1. **Primary repeat key = customerID** (merge-followed via `mergedIntoCustomerID`) — count delivered orders per customer.
2. **Light "possible duplicate" second pass** on normalised **phone**, then email, then name+postcode, to recover the ~127 fragmented identities. Don't over-build — payoff is small.
3. Surface both sub-cohorts (with / without realised history), with columns: realised Y/N, order count, realised LTV, first/last order, **months-since-last-order ("not trading since")**, repeat flag.
4. Headline views to expose: **repeat base (1,216 / £1.33M)** and **dormant-repeater win-back (446)**. The "personal VIP / high-frequency" outliers are the pattern-detection angle Ben raised.

Add to acceptance criteria: repeat detection primarily via customerID with an optional phone/email/name duplicate pass; expose the dormant-repeater win-back list.
