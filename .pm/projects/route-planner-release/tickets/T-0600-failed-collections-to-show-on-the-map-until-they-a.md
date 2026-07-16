---
id: T-0600
title: Show open failed collections on the sketch map until completed
type: feature
state: review
priority: p1
created: 2026-07-16T17:01:32Z
updated: 2026-07-16T18:04:51Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 7168
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Open failed collections appear on the sketch map with a visually distinct marker and an info popup (contract, postcode, weight, days outstanding)
  - They appear on every planning date until the collection is completed, then disappear
  - Map performance unaffected on a normal day (failed set is small; no extra geocoding round-trips for cached addresses)
out_of_scope: []
code_anchors:
  - path: backend/web/js/SketchPlanner.js
    note: leaflet map rendering — add failed-collection marker layer
  - path: backend/controllers/RoutePlannerController.php
    note: sketch view data / geocode endpoint — supply failed collections for date
  - path: common/models/YaRunContracts.php
    note: failed-collection status semantics (rc_status / failed flag used by getStopStatus)
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1801
    model: claude-fable-5
    started: 2026-07-16T18:01:56Z
    status: completed
    ended: 2026-07-16T18:04:51Z
    summary: "Failed collections now show on the sketch planner map. Any collection that failed on a past run and hasn't been cleared or extended (exactly the same selection as the /logistics/failed-jobs page, per Austin) appears as a red warning pin on the map — every planning day until it's dealt with — so logistics can see when a route passes near one and opportunistically add the collection to it. Clicking the pin shows the contract number, address, weight/volume, and how many days the collection has been outstanding. Adding it to a route is done through the normal contract search; the map's job is making the opportunity visible. The pins don't affect the map zoom (a far-away failure won't zoom your London plan out), and missing coordinates geocode once and cache on the address. Verified the selection on the sandbox: 4 open failed collections currently match."
    test_plan: |-
      1. Pull branch on sandbox, hard-refresh, open the sketch Map on any date: red pins appear for the open failed collections (sandbox currently has 4: C092821 W1W, C090838 N8, C093021 W11, C092990 N8 — two are at the depot postcode, they'll sit on the depot marker).
      2. Click a pin: popup shows FAILED COLLECTION header, contract no, address line, postcode, kg/m³, days outstanding.
      3. Clear one on /logistics/failed-jobs (clearFailed) → reload sketch → its pin is gone.
      4. Zoom check: pins outside the plan area don't change the initial map framing.
      5. First load after pull may take a moment while missing postcodes geocode (then cached on the address rows).
      Cross-impact: sketch page load does one extra query + up to N one-off geocodes; /logistics/failed-jobs behaviour untouched (read-only reuse of its criteria).
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
  since: 2026-07-16T18:04:51Z
version: 6
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
Failed collections (attempted but not completed on an earlier day) are invisible while planning. Logistics want them ON the sketch map — every day until they're done — so when a route passes near one they can opportunistically add it to that route.

## Fix shape
- Server: for the plan date, fetch collections in a failed/not-yet-completed state (regardless of original date), with lat/lng (geocode via the existing address cache) and contract details.
- Map: render them as distinct markers (e.g. red warning pins) with a popup (contract no, postcode, weight/volume, days outstanding). Display-only for v1 — adding one to a route is done by the normal search/add flow; the map just makes them visible.
- They disappear from the map once completed.

## Conversation

**2026-07-16 18:04 claude-code:** Run run-20260716-1801 completed — Failed collections now show on the sketch planner map. Any collection that failed on a past run and hasn't been cleared or extended (exactly the same selection as the /logistics/failed-jobs page, per Austin) appears as a red warning pin on the map — every planning day until it's dealt with — so logistics can see when a route passes near one and opportunistically add the collection to it. Clicking the pin shows the contract number, address, weight/volume, and how many days the collection has been outstanding. Adding it to a route is done through the normal contract search; the map's job is making the opportunity visible. The pins don't affect the map zoom (a far-away failure won't zoom your London plan out), and missing coordinates geocode once and cache on the address. Verified the selection on the sandbox: 4 open failed collections currently match.
