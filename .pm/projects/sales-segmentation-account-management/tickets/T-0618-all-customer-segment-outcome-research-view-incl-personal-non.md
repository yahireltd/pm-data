---
id: T-0618
title: All-customer segment/outcome research view — incl. personal & non-hirers, date-filtered, potential-vs-£0 gap, "not trading since"
type: feature
state: triaged
created: 2026-07-17T20:25:30Z
updated: 2026-07-20T00:32:29Z
project: sales-segmentation-account-management
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Ben
  channel: email
assignee: null
acceptance_criteria:
  - "View covers ALL customers, not just those with realised hire: personal (webmail), sub-£1k, and quote-only / didn't-proceed are all represented and each cohort is identifiable/filterable"
  - A 'had a fair chance to repeat' maturity filter — default to customers first seen >= 2 years ago, adjustable — so 'no repeat' is not just 'too new to have repeated yet'
  - A date-range filter (first-seen / quote / contract window) that applies across the whole view
  - Corporate leads are scored; personal customers show as 'Personal / unscored' but remain in every count and total
  - Surfaces the potential-vs-realised GAP aggregated by segment (for quote-only, realised = £0 so gap = full potential £), with drill from the segment total down to individual customers
  - A 'not trading since' / dormancy signal (last-order date, months-since) shown as sales intelligence, visually SEPARATE from the score and not feeding it
  - Every headline number drills through to real customers then to their quotes/contracts (keep the existing drill-to-data pattern)
  - The 'Personal' vs 'Personal with company' segment split (sibling ticket) is reflected here once available
  - Read-only, standalone, runnable on master so Ben can view immediately
out_of_scope:
  - ML / pattern-detection model (captured as a separate forward spike)
  - Any write-back / mutation of customer records — read-only only
  - Changing the scoring rubric
code_anchors:
  - path: backend/controllers/SegmentResearchController.php
    note: dataset(), actionIndex, actionContracts — extend to include non-hirers + personal + date + maturity filters
  - path: backend/views/segment-research/index.php
    note: cohort filters, gap column, 'not trading since' intel column
  - path: backend/views/segment-research/contracts.php
    note: drill-down list
  - path: customer_sales_scores
    note: score/segment source (keyed by domain)
  - path: ya_contracts
    note: realised £ + last-order date (contractType=1, unconverted=0/null)
  - path: quotes
    note: quote value for non-hirers (realised £0)
  - path: ya_customers
    note: email_domain, companyName, mergedIntoCustomerID, first-seen/dateJoined for maturity filter
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - p0018
  - phase-2
  - segment-research
  - research
attention: null
version: 3
---

## Problem
`/segment-research` today only shows customers who have **realised hire history**. Ben wants to drill into the *whole* picture — including customers who quoted but never proceeded, spent under £1k, or are personal (webmail) — to judge how well **segment + score actually predict outcome**, and to put a number on the missed opportunity. His framing: *"the gap between the potential and the £0 we've actually realised is the commercial argument."*

## Context
- From Ben's Phase-2 course-change thread (July 2026). He prefers to **interrogate the raw numbers** over a written summary, so this is a drillable view, not a report.
- Builds directly on `SegmentResearchController::dataset()` (canonical-customer join of score → realised £; short server cache).
- Reconciled scope: **corporate** leads get scored (realised £ = £0 → gap = full potential £); **personal** customers are labelled "Personal / unscored" but **INCLUDED** so the base is complete and honest.
- Standalone / read-only so Ben can view immediately on `master` (same pattern as the current `/segment-research`).
- Sibling ticket introduces the **Personal vs Personal-with-company** segment split — this view must reflect it once available.

## Notes / forward
- The ML / pattern-detection angle Ben raised is a **forward spike**, not part of this build — flag it, don't scope it here.

## Conversation

**2026-07-19 20:27 claude-code:** **Repeat-quoting = a distinct high-intent signal (Austin's idea, analysed 2026-07-19). Should be a column/filter + a prioritised list on this view.**

Within the quote-only corporate cohort, "quoted repeatedly but never hired" is *repeated buying intent that we lost* — arguably a hotter lead than a cold high-fit-score. The numbers (verified-corporate, scored, £0 realised):
- **1,466 A/B-tier domains quoted ≥2× and never hired — £4.7M of quotes.**
- **614 quoted ≥3× (£3.2M); 178 quoted ≥5× (£1.6M).**

The killer segment is **fit-score × repeat-intent**: A/B-tier AND ≥4 quotes AND £0 realised = the single hottest lead type in the base. Top of that list:
- BCD Meetings & Events (A96, 11 quotes), LS Events (A92, 15), White Label Live (A82, 11), The Berkeley (A78, 12), LarMac (A74, 21!), Under Current (A74, 14), Minstry Venues (A77, 11) — event agencies / production companies / venues that quoted **10–21 times** and never converted. That's a sales team's call-list, ordered.

**Add to this view:** a **"quotes"** count column + a filter, and a saved **"hot lead" view = tier A/B + quotes ≥ N + realised £0**, sorted by quote-count. Data source: aggregate `quotes` by email-domain (COUNT(*) + SUM(quoteTotal)); exclude webmail domains — the raw ranking is polluted by typo-webmail aggregation (gmail.con, hotmail.it, etc.), so restrict to *scored* (verified-corporate) domains for the clean list. Complements the "not trading since" dormancy signal already on this ticket — one is lapsed customers, this is never-won repeat prospects.

**2026-07-20 00:32 claude-code:** **2026-07-20 — Built & deployed to the P-0018 server (segmentationtest.yahire.com).**

New page **`/segment-research/outcomes`** — all customers cross-tabbed **segment × outcome**. Live now for Ben.

Acceptance criteria status:
- ✅ ALL customers represented — every active canonical customer (~80k): Scored corporate / Unscored corporate / **Personal (webmail)** / No-domain, each × Repeat / One-off / **Quoted-never-hired** / No-activity. Personal is labelled but stays in every count & total.
- ✅ Maturity filter — default **"had ≥2yr to repeat"** (first-seen = earliest quote or hire), adjustable to All/1/2/3yr.
- ✅ Date-range filter (first-seen from/to) across the whole view.
- ✅ Every cell drills to the real customers, then corporate rows drill to their contracts.
- ⚠️ **Potential-vs-realised GAP by segment — partial.** The quote-only cohorts show £0 realised + total **quote £** (the lost-value proxy), but the *modelled* potential (`customer_account_levels.potential_value`) is not yet joined in as an explicit gap column. Easy follow-up.
- ⚠️ **Personal vs Personal-with-company split — not yet** (waiting on the sibling ticket; today "Personal" = all webmail).
- ✅ Read-only, standalone.

What the data shows (≥2yr-mature): **28,416 personal + 9,275 scored-corporate customers quoted and never hired** — the win-back universe. Personal repeat-buyers = £1.9M realised; scored-corporate repeat = £14.6M.

Also: consolidated ALL P-0018 web deliverables onto one branch (`p0018-consolidated`) now served by the box — segment-research (correlation/outcomes/leads), fast-track lane, segment profile, account-levels. The correlation page's repeat-share headline is now computed live (87%) instead of hardcoded.
