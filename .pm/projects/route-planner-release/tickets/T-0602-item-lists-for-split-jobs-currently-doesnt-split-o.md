---
id: T-0602
title: Item lists for split jobs - currently doesnt split on the item level until finalise. We should do this before finalise so it isnt confusing - particularly when loading a live plan that was pre-split from logistics
type: chore
state: triaged
priority: p2
created: 2026-07-16T17:02:00Z
updated: 2026-07-16T17:07:04Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 9216
reporter: null
assignee: null
acceptance_criteria:
  - On the sketch planner, when we auto split (or existing splits from the live plan) they show the full item list from the contract rather than what is actually meant to be on that vehicle according to the weight and volume.
  - The item split is currenltly done on the finalise but we would perfer the item list to be shown correctly on the sketch planner before finalise
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 4
---

## Problem

_From meeting M-001._

Item lists for split jobs - currently doesnt split on the item level until finalise. We should do this before finalise so it isnt confusing - particularly when loading a live plan that was pre-split from logistics
