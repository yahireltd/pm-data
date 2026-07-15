---
id: MS-003
slug: document-generation-integrity-on-live-t-0538
title: Document generation integrity on live (T-0538)
project: accounts-integrity
state: planned
order: 3072
created: 2026-07-10T21:16:32Z
updated: 2026-07-15T17:17:28Z
acceptance_criteria:
  - Branch merged (after/with T-0534) and deployed
  - RBAC route permission granted for /doc-integrity; xeroOrgShortCode param set on live
  - Validations page shows 100% PASS for documents generated in the first 2 weeks
  - invoice-integrity log channel quiet or triaged same-day
  - Austin has completed the deep-dive sampling checklist (4 parts)
  - "Website generators brought up to date (T-0540): yahirenew + chl generate documents that pass the same header==lines checks — no old-engine documents flowing in from customer payments (milestone cannot be hit while they do)"
  - "T-0426 (refund method gate) rides in the SAME 538 branch and must be regression-tested as part of this release, not just T-0538: refund-method choice only on genuine credit-only refunds; surplus refunds go by their own method; deposit-refund flow behaviour confirmed or consciously deferred"
slip_records: []
target_date: 2026-08-07
version: 5
phase_id: PH-005
---

# Document generation integrity on live (T-0538)

## Acceptance criteria

## Notes
