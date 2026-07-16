---
id: T-0606
title: Run planner may not respect sales-entered job minutes — check split and non-split paths (Nana, UAT meeting)
type: bug
state: review
created: 2026-07-16T17:28:13Z
updated: 2026-07-16T18:00:30Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Nana (via Austin, M-001 UAT meeting)
  channel: meeting
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "Reproduction documented: what sales enters vs what the run planner shows, for one non-split and one split contract"
  - Root cause identified (incl. whether T-0379's assignment bug is the cause)
  - Fix applied so non-split jobs always show sales-entered minutes; split behaviour agreed with logistics and implemented
  - Nana confirms the reported case now shows the right minutes
out_of_scope: []
code_anchors:
  - path: common/models/YaRuns.php
    note: getLoadUnLoadMinutes — known `=` vs `==` assignment bug (T-0379)
  - path: backend/views/logistics/run-planner.php
    note: job minutes display
  - path: common/services/SketchPlanService.php
    note: finalise writes run contracts incl. jobMins
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1759
    model: claude-fable-5
    started: 2026-07-16T17:59:35Z
    status: completed
    ended: 2026-07-16T18:00:30Z
    summary: "Investigated Nana's report that the run planner ignores sales-entered job minutes. Findings: (1) For NON-split jobs the planner DOES respect the sales duration — it acts as a minimum over the weight/volume estimate — unless someone manually set per-row minutes on the run planner, which deliberately override everything. (2) For SPLIT jobs the sales duration is divided between the pieces in proportion to each piece's share of weight+volume — so a 60-minute entry becomes ~30 minutes per half. If the sales entry represents fixed site time (parking, access, finding the contact) rather than time-that-scales-with-load, every piece visit is understated. This matches what Nana saw and is a POLICY QUESTION for logistics, parked for decision (options: keep proportional; full duration per piece; or proportional with a per-piece minimum). (3) Fixed an unrelated-looking but real bug found on the way (the known T-0379 issue): the depot load/unload minutes helper used assignment instead of comparison, so UNLOAD time was always computed from the morning's delivery load instead of the collections coming back — corrected."
    test_plan: |-
      1. Non-split check (Nana's first case): enter a duration on a contract in sales (e.g. 45m), plan it, check the run planner stop shows ≥45m. If Nana can reproduce her original case, capture the contract number — most likely explanations are a manual per-row override (jobMins set) or the entry being on the other direction (durationDel vs durationCol).
      2. Split check: a split contract with a sales duration shows each piece with its proportional share (e.g. 60m → ~30m+30m for equal halves). Confirm with logistics whether that's wanted — DECISION PENDING with Austin/logistics: proportional (current), full per piece, or proportional with a floor.
      3. Unload-minutes fix: a run with big collections but small deliveries now shows unload time based on what comes BACK (end load), not what went out.
      Cross-impact: getLoadUnLoadMinutes callers (run planner ETA displays) now get different (correct) Unload values — expect evening depot times to shift slightly on collection-heavy runs.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - release-blocker
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-16T18:00:30Z
version: 4
---

## Problem
From M-001 (16 Jul UAT meeting) outcomes: "Nana mentioned the original run planner not respecting the job minutes entered by sales — check this isn't only when it is split and even if it is when split is this the right behaviour". This outcome was recorded but had no follow-up ticket — filing it so it doesn't get lost before Monday's release.

## What to establish
1. Reproduce: does a job's sales-entered minutes (jobMins / duration on the contract) carry through to the run planner row for a NON-split job?
2. For a SPLIT job: what happens to the minutes on each piece — copied verbatim (duplicating time), divided, or dropped to a default? The solver splits service time across pieces (service_special_piece_sec etc.) — check the run planner path does something consistent.
3. Decide the right behaviour for split pieces with logistics and implement.

## Context
Likely related to the known getLoadUnLoadMinutes assignment bug (T-0379, `=` vs `==`) which silently overwrites minutes. Check that first — it may be the whole story.

## Conversation

**2026-07-16 18:00 claude-code:** Run run-20260716-1759 completed — Investigated Nana's report that the run planner ignores sales-entered job minutes. Findings: (1) For NON-split jobs the planner DOES respect the sales duration — it acts as a minimum over the weight/volume estimate — unless someone manually set per-row minutes on the run planner, which deliberately override everything. (2) For SPLIT jobs the sales duration is divided between the pieces in proportion to each piece's share of weight+volume — so a 60-minute entry becomes ~30 minutes per half. If the sales entry represents fixed site time (parking, access, finding the contact) rather than time-that-scales-with-load, every piece visit is understated. This matches what Nana saw and is a POLICY QUESTION for logistics, parked for decision (options: keep proportional; full duration per piece; or proportional with a per-piece minimum). (3) Fixed an unrelated-looking but real bug found on the way (the known T-0379 issue): the depot load/unload minutes helper used assignment instead of comparison, so UNLOAD time was always computed from the morning's delivery load instead of the collections coming back — corrected.
