---
id: T-0599
title: Historical data cleanup — the leftover-contract fix list (each item, how to fix, prevention status)
type: chore
state: triaged
created: 2026-07-16T14:41:08Z
updated: 2026-07-16T14:41:08Z
project: accounts-integrity
section: null
parent: null
milestone: MS-004
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee: null
acceptance_criteria:
  - C091771/78307 confirmed corrected on LIVE (251316 = -180, 78307 coherent) or the idempotent SQL run + reposted
  - "Test docs voided: 75664, 70029, 70030 locally; INV 69663 voided in live Xero"
  - "Decision recorded on the 5 legacy 2024 credit items (recommend: leave)"
  - "Prevention gap acknowledged: root-cause fixes ride with 538 (not yet live); T-0542 covers test-contract leakage"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - data-fix
  - accounts
  - cleanup
attention: null
version: 1
---

## What this is

The definitive list of historical documents/items still needing a manual correction, verified against the data on 2026-07-16. Each row: what's wrong, how to fix, and whether the CAUSE is prevented going forward. **Key context: every root-cause prevention lives in the 538 branch, which is NOT yet live — until 538 ships, these classes can still recur on live (the repair-docs button + posting safety net catch the symptoms, but not the root cause).**

## The list

### A. REAL customer — the only genuinely broken live-era customer document
**C091771 / credit note 78307 (ARC, finance@arc-conference.com)** — Paula typed a negative compensation which double-negated to +£180, and the old adjuster smeared the Collection line (−£300 net / −£60 VAT on a 1×£60 line — the sign-mismatched tax Xero rejects).
- **Fix (idempotent SQL, rehearsed & Xero-accepted on sandbox):** set item 251316 priceFixed −180; set its 5 archived copies (itemRef 36c70442, contractRef b100b876b21c4b03b5aef5459e8b00e5, stockID 788) priceFixed −180; set 78307 line 92784 to −180/−180/−36; set line 92788 (Collection) to net 60 / vat 12. Then `php yii xero-post/daily --date=2026-07-01`.
- **VERIFY ON LIVE FIRST** — on the sandbox this already shows fixed (251316 = −180, 78307 coherent at 29.52/5.90), but the sandbox refreshes from live nightly AND I applied it there during rehearsal, so I can't tell if the LIVE fix ran. Verification query on live: `SELECT priceFixed FROM ya_contract_items WHERE id=251316;` (want −180.00) and the 78307 coherence check. If already −180, done; the SQL is a no-op if re-run.
- **Prevention:** sign guard in createCreditNoteItem (`-abs()`) + adjuster rework — **both in 538, NOT live**. Until 538 ships a mistyped negative comp can still flip positive on live.

### B. Internal TEST contracts — void, do NOT reissue
These are staff test contracts (all @yahire.com). They leaked into the real books; the correct disposition is voiding, not correcting.
- **C089510 / CN 75664 (zsolt@yahire.com)** — void locally. Its partner **INV 69663** reached live Xero as a £604.36 Awaiting-Payment receivable — **void it in Xero** (confirm current status; Austin was going to). 
- **C089676 / INV 70029 + CN 70030 (austin@yahire.com)** — bundle-test contract, £3,412 headers vs £99 lines. Void both locally (neither posted to Xero).
- **Prevention:** T-0542 (keep test contracts out of Xero) — NOT built; direction to be picked at the meeting (rec: auto-exclude @yahire.com + test-server policy).

### C. Legacy 2024 credit items — LEAVE, documented (recommendation)
Five pre-go-live (July–Aug 2024) credit items with a positive priceFixed where quoted is negative — real customers, old (header-only) accounting, do NOT feed the itemised engine, produce no broken new-accounting documents:
- 149523 C074620, 150107 C074706, 150796 C074820, 151969 C075022, 154599 C075455.
- **Recommendation: leave as-is** (changing historical old-accounting records has its own desync risk and no live symptom). Record the decision. Fix only if accounts flags a specific one.

## Answers to the standing questions
- **"Fix data button"?** Yes — `/xero/repair-docs` (+ XeroDocRepairService) is LIVE (shipped in 534/master). BUT it makes a document POSTABLE by adding/splitting labelled adjustment lines so header==lines — it is NOT a root-cause data editor. So: test contracts → void (not repair); 78307 → the data SQL (repair-docs would make it postable but leave the £180 wrong). 
- **Charge-history fix (T-0545) done?** NO. Re-resolution still DELETEs invoiced charge/comp items (IssueReportsController::actionResolveItemIssue, 4 delete() calls); only the CANCEL path reverses properly. Needs Fran's decision (meeting item 7) then code. Nuance: the deletion IS auto-logged ('Item Deleted From Contract') so history isn't wholly gone — what's lost is the issue-level context + Fran's "keep the charge as a reversal" preference.

## Acceptance
Each A/B item dispositioned (fixed-and-verified, or voided); C decision recorded; live-vs-538 prevention gap understood (i.e. these are one-offs, the durable prevention ships with 538/T-0542).