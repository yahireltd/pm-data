---
id: T-0532
title: Drop the "no breaks before 10:00" floor (Feb workaround, not a real policy)
type: chore
state: done
created: 2026-07-08T18:38:35Z
updated: 2026-07-13T16:54:19Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Replay of run #2495 (10 Jul): 0 drops, all required breaks still scheduled"
  - "[x] Early far runs (e.g. New Forest) no longer get artificially delayed dispatches purely to push the break past 10:00"
  - "[x] A break may now legally start before 10:00 when ≥3h active has accumulated"
out_of_scope: []
code_anchors:
  - path: backend/assets/RoutePlannerPython/solver_td.py:31
    note: MIN_BREAK_START_HOUR constant + _schedule_break gate
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1838
    model: claude-fable-5
    started: 2026-07-08T18:38:53Z
    status: completed
    ended: 2026-07-08T18:43:58Z
    summary: "Removed the hidden \"no driver breaks before 10:00\" rule that had constrained every plan since February. Austin confirmed it was a workaround from early break-scheduling problems, not a business or legal rule — the real protections (no break before 3 hours of work, at least an hour of work after, drivers'-hours trigger) all remain. With the floor gone, early-starting routes take their break naturally when the 3 hours are up instead of the solver contorting the plan around 10:00 (artificially delayed departures, end-of-route or mid-drive breaks). Kept as a one-number dial so it can be restored or softened if drivers ever push back. Verified by replaying the 10 July request: still 0 dropped jobs across 16 routes, with two breaks now sitting naturally at 08:40 and 09:46 and fewer forced mid-drive placements."
    test_plan: |-
      1. Re-solve an early-dispatch day (one with 05:30-07:00 starts, e.g. 10 or 13 Jul): breaks may now appear between 08:30-10:00 on early routes — that's correct, not a bug; each still has ≥3h work before it and ≥1h after.
      2. Far runs like New Forest should no longer show artificially late dispatches that existed only to push the break past 10:00 (compare dispatch time to the previous solve of the same day).
      3. Confirm 0 unassigned still holds on re-solve.
      4. If drivers/ops complain about pre-10:00 breaks, the dial is MIN_BREAK_START_HOUR in solver_td.py (repo copy at backend/assets/RoutePlannerPython/, deploy = scp + restart vrp-solver) — set an hour and it re-engages, including the delayed-dispatch machinery which is dormant, not deleted.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - solver
  - route-planner
attention: null
version: 9
---

## Problem
MIN_BREAK_START_HOUR = 10 has silently constrained every plan since the original TD-solver build (25 Feb 2026). Austin: "lets drop that as it seems like a really strange parameter - i cant see any reason why. I was really having issues back then getting the breaks to schedule" — i.e. it was a workaround for early break-scheduling problems, not an ops rule. It is not a legal requirement (the 5h-active trigger models drivers'-hours; the 3h-active minimum already prevents silly-early breaks), and it is the main reason the solver resorts to delayed dispatches and lower-tier break placements (end-of-route, mid-drive) on early far runs.

## Fix
Set MIN_BREAK_START_HOUR = 0 (kept as a dial rather than deleting the machinery, so it can be restored/softened by changing one number). With the floor at 0 the delayed-dispatch tier goes dormant naturally and breaks schedule at the earliest slot satisfying the remaining gates: ≥3h active before, ≥1h work after, all windows/shift limits.

## Conversation

**2026-07-08 18:43 claude-code:** Run run-20260708-1838 completed — Removed the hidden "no driver breaks before 10:00" rule that had constrained every plan since February. Austin confirmed it was a workaround from early break-scheduling problems, not a business or legal rule — the real protections (no break before 3 hours of work, at least an hour of work after, drivers'-hours trigger) all remain. With the floor gone, early-starting routes take their break naturally when the 3 hours are up instead of the solver contorting the plan around 10:00 (artificially delayed departures, end-of-route or mid-drive breaks). Kept as a one-number dial so it can be restored or softened if drivers ever push back. Verified by replaying the 10 July request: still 0 dropped jobs across 16 routes, with two breaks now sitting naturally at 08:40 and 09:46 and fewer forced mid-drive placements.

---

**2026-07-13 16:54 — you**

Done
