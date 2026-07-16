---
id: T-0601
title: "Sketch planner: show the full job address (not just postcode) on stop expand"
type: feature
state: triaged
priority: p1
created: 2026-07-16T17:01:50Z
updated: 2026-07-16T17:28:33Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 8192
reporter: null
assignee: null
acceptance_criteria:
  - Expanding any stop card shows the full address lines for that stop's type (delivery vs collection address, not always delivery)
  - Works for both pool (unassigned) and on-route stop cards
  - No extra HTTP round-trip per expand — address rides with the existing contractDetails payload
out_of_scope: []
code_anchors:
  - path: backend/views/route-planner/sketch-planner.php
    note: stop expand panel (toggleExpand/isExpanded around lines 659-780)
  - path: backend/controllers/RoutePlannerController.php
    note: contractDetails assembly for the sketch view
  - path: common/models/YaContracts.php:575-580
    note: deliveryAddress/collectionAddress relations → YaContractAddr (addr1, town_city, postcode)
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - route-planner
  - ui
  - release-blocker
attention: null
version: 3
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
"Full address of job not visible anywhere on the sketch planner" — planners only see the postcode on stop cards, so distinguishing two jobs in the same postcode (or sanity-checking an odd geocode) means opening the contract in another tab.

## Fix
Surface the full delivery/collection address (addr1/addr2, town, postcode) in the stop card's expand panel (the chevron toggle already fetches contractDetails) — and in the map marker popup if cheap. Data lives on YaContractAddr via the contract's deliveryAddress/collectionAddress relations; pick the address matching the stop type (D → delivery, C → collection).