---
id: T-0426
title: Bacs refund happened when stripe was selected
type: feature
state: triaged
priority: p2
created: 2026-06-18T15:44:12Z
updated: 2026-07-15T15:48:28Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 36864
reporter: null
assignee: null
acceptance_criteria:
  - Look into the cause as to why this happened
  - propose the correct fix (do not touch the code until we have agreed on the solution)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 4
---

https\://system.yahire.com/contracts/manual-refund?quoteOrContractNumber=C090850 ben chose stripe and it refunded by BACS anyway

## Conversation

**2026-07-15 15:48 — Austin Pickering**

Ben just tired 2 more of these. He selected stripe for 2 different refunds. One went by bacs and one went by stripe. It seems if the first payment on a contrat is BACS it will refund by bacs regardless of if we pick stripe. (obviously if we do refund to stripe there needs to be money left to refund on it (and if the deposit was paid by stripe in the same transaction as the payment we must reserve the deposit portion on stripe to refund) I thought i had taken care of this already but seems to be an issue with it
