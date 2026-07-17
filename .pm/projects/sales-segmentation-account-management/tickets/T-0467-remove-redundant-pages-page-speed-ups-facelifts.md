---
id: T-0467
title: Remove Redundant Pages, Page Speed-ups, facelifts
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:47:31Z
updated: 2026-07-17T06:37:55Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 2048
reporter: null
assignee: null
acceptance_criteria:
  - A Nathan-approved list of pages to retire or merge is produced, backed by last-2-months usage data.
  - "Leads, Website Quotes, and Key Account Requests (incl. its QB icon) are each decided: retire, merge, or keep (with reason)."
  - The Smartest View / Ledger overlap on contracts is resolved (merge, or keep-both with rationale).
  - Website Quotes removal is sequenced after the contract-checking page (T-0468) is live.
  - High-traffic slow pages identified from usage are queued for speed-ups.
out_of_scope: []
code_anchors: []
relates:
  - T-0468
  - T-0608
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 6
---

## Problem
Sales/management screens have accumulated pages that are redundant, duplicated, or rarely used. We need a Nathan-approved list of what to retire or merge, backed by usage data. Also covers page speed-ups / facelifts on high-traffic slow pages.

## Redundant / merge candidates (from M-010, Nathan)
- **Leads** — candidate to retire.
- **Website Quotes** — retire once a dedicated contract-checking page exists (see T-0468).
- **Key Account Requests** — retire, including its icon on the Quote Builder.
- **Smartest View ↔ Ledger** — possible overlap, specifically around contracts; candidate to merge.

## Approach
Zsolt to summarise the sales + management (sales-related) pages and compare against the last 2 months' usage to see what's actually in use, then get Nathan's input on which can be merged/retired.
