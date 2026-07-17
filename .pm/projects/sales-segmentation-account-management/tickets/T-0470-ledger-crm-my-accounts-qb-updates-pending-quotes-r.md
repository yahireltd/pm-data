---
id: T-0470
title: Ledger / CRM / My Accounts / QB Updates / Pending Quotes Review
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:51:35Z
updated: 2026-07-17T06:34:36Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 5120
reporter: null
assignee: null
acceptance_criteria:
  - Ledger shows contract value (not just quote value) for contracted work.
  - Quick fixes for the top daily bug-bears across Ledger/CRM/Profile/QB/Pending are identified and actioned.
  - My Accounts overhaul is explicitly deferred until the relationship-management process is defined.
  - Cancelled-commission rules on team targets are clarified (paid vs invoiced).
out_of_scope: []
code_anchors: []
relates:
  - T-0551
  - T-0607
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

## Problem
Everyday review of the main pages (Ledger / CRM / Customer Profile / Quote Builder / Pending Quotes) — low-hanging fruit, daily bug-bears, and pages needing an overhaul. Quick updates to bring things up to speed (not a full overhaul while processes are still developing). To be repeated with users.

## From M-010 (Nathan)
- **My Accounts page** needs work, but **later** — once the relationship-management process is clearer (Austin/Zsolt).
- **Ledger bug:** shows only quote value (which can be cancelled or change), not **contract value** — it should show contract value.
- **Cancelled commission** on team targets needs review and clear rules/logic, including paid and invoiced. (Specific instance: a split contract cancelled to £0 still shows £666 on Paula's Q2 — tracked as its own bug, see linked.)
- Nathan asked about **customer discounts on delivery prices** → covered by T-0551.
