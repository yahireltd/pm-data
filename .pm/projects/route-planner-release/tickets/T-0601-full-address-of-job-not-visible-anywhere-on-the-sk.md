---
id: T-0601
title: "Sketch planner: show the full job address (not just postcode) on stop expand"
type: feature
state: review
priority: p1
created: 2026-07-16T17:01:50Z
updated: 2026-07-16T17:40:40Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 8192
reporter: null
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260716-1732
    model: claude-fable-5
    started: 2026-07-16T17:32:09Z
    status: completed
    ended: 2026-07-16T17:40:40Z
    summary: "Made stop addresses impossible to miss on the sketch planner. Investigation first: the full address was ALREADY on the stop expand panel and in the data payload (Austin confirmed the outcome was recorded without checking) — the problem was prominence, not absence. Changes: the first address line now renders bold with a red map-marker icon in the expand panel (both the unassigned pool and route cards), and the map now shows the bold first address line above the postcode in every marker popup (route stops and unassigned). The first line is sent as its own field from the server so the bolding is exact even when addresses contain commas."
    test_plan: |-
      1. Pull branch on sandbox, hard-refresh (JS changed). Expand any stop card (chevron, both a pool card and an on-route card): address row shows a RED marker icon and the first line in bold, remainder in normal weight.
      2. Delivery stops show the delivery address; collection stops the collection address (check a contract where they differ).
      3. Open the Map: click a route marker and an unassigned (grey) marker — popups show the bold first address line above the postcode.
      4. A contract with no address rows renders no empty address row (no stray red icon).
      Cross-impact: getContractDetails gained two fields (del_addr1/col_addr1) — additive; existing consumers unaffected.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - ui
  - release-blocker
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-16T17:40:40Z
version: 6
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
"Full address of job not visible anywhere on the sketch planner" — planners only see the postcode on stop cards, so distinguishing two jobs in the same postcode (or sanity-checking an odd geocode) means opening the contract in another tab.

## Fix
Surface the full delivery/collection address (addr1/addr2, town, postcode) in the stop card's expand panel (the chevron toggle already fetches contractDetails) — and in the map marker popup if cheap. Data lives on YaContractAddr via the contract's deliveryAddress/collectionAddress relations; pick the address matching the stop type (D → delivery, C → collection).

## Conversation

**2026-07-16 17:40 claude-code:** Run run-20260716-1732 completed — Made stop addresses impossible to miss on the sketch planner. Investigation first: the full address was ALREADY on the stop expand panel and in the data payload (Austin confirmed the outcome was recorded without checking) — the problem was prominence, not absence. Changes: the first address line now renders bold with a red map-marker icon in the expand panel (both the unassigned pool and route cards), and the map now shows the bold first address line above the postcode in every marker popup (route stops and unassigned). The first line is sent as its own field from the server so the bolding is exact even when addresses contain commas.
