---
id: T-0470
title: Ledger / CRM / My Accounts / QB Updates / Pending Quotes Review
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:51:35Z
updated: 2026-07-20T06:16:53Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 5120
reporter: null
assignee: null
acceptance_criteria:
  - Decision recorded on whether the Ledger should surface contract value (it shows quotes only by design today) — a design call, not a bug fix.
  - Quick fixes for the top daily bug-bears across Ledger/CRM/Profile/QB/Pending are identified and actioned.
  - My Accounts overhaul is explicitly deferred until the relationship-management process is defined.
  - Cancelled-commission rules on team targets are clarified (paid vs invoiced) — meeting decision.
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
version: 7
---

## Problem
Everyday review of the main pages (Ledger / CRM / Customer Profile / Quote Builder / Pending Quotes) — low-hanging fruit, daily bug-bears, and pages needing an overhaul. Quick updates to bring things up to speed (not a full overhaul while processes are still developing). To be repeated with users.

## From M-010 (Nathan)
- **My Accounts page** needs work, but **later** — once the relationship-management process is clearer (Austin/Zsolt).
- **Ledger — design note (NOT a bug):** the Ledger shows **quotes only, by design**; it doesn't reflect contract value (and quote value can be cancelled or change). Whether to surface contract value on the Ledger is a **design decision for Nathan**, not a fix.
- **Cancelled commission** on team targets needs review and clear rules/logic, including paid and invoiced. (The specific £666 instance — a split contract cancelled to £0 still showing on Paula's Q2 — was fixed under **T-0607**. The paid-vs-invoiced rule itself is a separate meeting item.)
- Nathan asked about **customer discounts on delivery prices** → covered by T-0551.

## Conversation

**2026-07-20 06:16 claude-code:** **Ledger stats — management note (relayed by Zsolt), 1 May–date figures review**

Context: For 1 May–date the Ledger's "converted quote value" (~£1.4m) and revenue (~£2.3m) don't match — and that's by design, not a bug. The converted-quote figure is **goods only and ex-VAT**: it takes the ex-VAT quote subtotal and strips out delivery, collection, cleaning and insurance. Revenue is the **full contract total** — goods + delivery/collection + surcharges + VAT. So they measure two different things; the ~£0.9m gap is those extra charges plus VAT.

Actions to fold into this milestone's Ledger work:

1. **Label "Revenue" with an explainer** — make explicit on-screen what it includes (full contract total, inc delivery/surcharges and VAT), so it isn't read as comparable to the quote/goods value.
2. **Revise the stats to consider Quote value vs Contract value (Q vs C)** — and work out how best to incorporate total contract value alongside the goods/quote figures. Flagged as needs-thought before building.

Ties directly into the existing acceptance criterion on this ticket: *"Decision recorded on whether the Ledger should surface contract value (it shows quotes only by design today)."*
