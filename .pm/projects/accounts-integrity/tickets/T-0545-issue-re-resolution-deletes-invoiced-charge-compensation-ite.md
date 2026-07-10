---
id: T-0545
title: Issue re-resolution DELETES invoiced charge/compensation items instead of reversing them
type: bug
state: triaged
created: 2026-07-10T21:47:41Z
updated: 2026-07-10T21:47:41Z
project: accounts-integrity
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code (T-0538 review)
  channel: code review
assignee: null
acceptance_criteria:
  - "Business decision recorded: delete-and-recreate vs reverse-and-recreate on re-resolution"
  - "If reverse: re-resolution uses the same reversal construction as actionCancelChargeCompo (VAT-symmetric, contractType stamped), with tests"
  - Invoiced-item deletion path either removed or explicitly justified
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - issue-resolution
  - accounts
  - t-0538-follow-on
attention: null
version: 1
---

## Problem

When an issue is RE-resolved, `IssueReportsController::actionResolveItemIssue` (and the contract-level sibling; lines ~474, 515, 566, 602) **deletes** the existing 786–789 charge/compensation items outright — with no check whether they've already been invoiced. A code comment even says "Check with fran... would rather have the charge item remain".

Contrast: the CANCEL flow (`actionCancelChargeCompo`) does this properly — it keeps the original item and creates an opposite-signed reversal item (VAT-symmetric 786↔788 / 787↔789).

## Money impact (post-T-0538)
Deleting an invoiced item is money-CORRECT — the diff engine emits a removal credit on the next generation — but:
- the credit line references a deleted item (weaker traceability than a reversal pair),
- the acceptedchanges trail records only a generic deletion, losing the issue-level story,
- it contradicts the business preference in the comment (charge history should remain).

## Decision needed (Fran / accounts + Austin)
Should re-resolution mirror the cancel flow (reverse + create anew) instead of delete + create? If yes, small refactor to share the canceller construction.