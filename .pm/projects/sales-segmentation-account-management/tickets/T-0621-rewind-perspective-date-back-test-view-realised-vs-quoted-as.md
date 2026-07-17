---
id: T-0621
title: Rewind / perspective-date back-test view — realised vs quoted £ as-of any past date (validate score→£)
type: feature
state: triaged
created: 2026-07-17T21:31:18Z
updated: 2026-07-17T21:31:18Z
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
  - A 'perspective date' control (defaults to today; can pick any past date)
  - "As-of that date, per customer and aggregated by segment/score: realised £ = sum of contracts with delivery/hire date <= perspective date (contractType=1, unconverted=0/null); quoted £ = sum of quotes with createdDate <= perspective date"
  - A 'realised since' delta = realised between the perspective date and now — the actual outcome the score-then is being tested against
  - "Back-test framing: bucket customers by score/segment, show realised-since £ per bucket, so higher score/segment can be shown to (or not to) predict more realised-since £"
  - Drill from any bucket -> customers -> their contracts/quotes with dates (reuse the existing drill-to-data pattern)
  - Read-only, standalone; shares T-0618's dataset/join layer rather than duplicating it
  - The two date concepts are kept visibly distinct from T-0618's cohort date-range (no conflation)
out_of_scope:
  - True score-as-of-then (requires a score-history table; v1 uses current score) — follow-up if wanted
  - Any write-back / mutation
  - Folding into T-0618 (deliberately kept separate per Austin)
code_anchors:
  - path: backend/controllers/SegmentResearchController.php
    note: share dataset() score->£ join layer; add perspective-date param + as-of aggregation, or spin a sibling RewindController that reuses it
  - path: ya_contracts
    note: hireStartDate/delivery date + contractTotal -> as-of realised £ (contractType=1, unconverted=0/null)
  - path: quotes
    note: createdDate + quoteTotal -> as-of quoted £
  - path: customer_sales_scores
    note: score/segment; scored_at is the only temporal marker (no full history yet)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - p0018
  - phase-2
  - research
  - backtest
attention: null
version: 1
---

## Problem
We can see realised vs quoted £ as they stand *today*, but we can't **rewind the clock**. To actually validate the scoring effort we need to pick a **perspective date** and see, as of that date, each customer/segment's **realised £** (contracts delivered up to then) and **quoted £** (quotes raised up to then) — then compare against what they've realised *since*. "Did the score/segment we'd have assigned back then predict the £ that came after?" is the real proof Ben keeps asking for.

## Why standalone, not folded into T-0618 (Austin's steer: don't conflate views)
T-0618's date filter is a **cohort filter** — *which* customers/rows fall inside a window. This is a **different axis**: a point-in-time *recomputation of the £ metrics themselves* (an "as-of" clock), plus a since-then delta. Mixing an as-of clock with a cohort window in one screen is exactly the conflation to avoid. So: keep **T-0618 = "who is everyone / what's the gap, now"**; this ticket = **"rewind & back-test."** It should **share T-0618's score→£ dataset/join layer** (no duplicate plumbing) but present a distinct, time-travel view.

## Notes / dependency
- Historical *scores* are not versioned (`customer_sales_scores` has `scored_at` but no full as-of history), so v1 back-tests the **current** score/segment against realised-since. True score-as-of-then would need a score-history table — flag as a follow-up if wanted.