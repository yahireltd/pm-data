---
id: T-0219
title: Forcing the close of the run planner tab is overkill - we should be able to just take over without them closing it  and show the sketch planner active message on the run planner when sketch takes over
type: feature
state: review
priority: p2
created: 2026-06-04T14:04:09Z
updated: 2026-06-04T18:03:04Z
project: logistics-route-planning-rollout
section: null
parent: null
children: []
order: 3072
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - when trying to edit a sketch and the run planner is open we need to make sure that the run planner for that day is locked. It currently asks us to close the run planner tab - can we just make sure that it gets locked  (sketch planner active warning is fine we shouldnt need to actually close the tab as somone may have left it open on their machine)
  - Also when we go to edit a sketch and the run planner has a live plan- we should make sure that the sketch we are editing is the live plan (eg if we had both open (run planner in edit and skecth in view) then somone made some changes on the run planner those changes need to be visible on the sketch planner - perhaps we need to reload the live when going into edit on skecth planner)
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1414
    model: claude
    started: 2026-06-04T14:14:37Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention:
  needed_by: human
  reason: Implemented & committed (8b37aef5); ready for human review/testing — test steps in the Conversation.
  since: 2026-06-04T18:03:04Z
---

## Problem

_Suggested feature from meeting M-003._

Forcing the close of the run planner tab is overkill - we should be able to just take over without them closing it  and show the sketch planner active message on the run planner when sketch takes over

## Conversation

**2026-06-04 18:02 claude-code:** **Done** — committed `8b37aef5`.

The sketch now **supersedes** the run-planner session instead of asking you to close the tab (`PlannerEditingSession::acquireSession` releases the active `run` session when a `sketch` acquires). The run planner already polls session status and self-locks with a "Sketch Planner Active" modal. Entering edit also **mirrors the live run plan in place** (`actionSketchReopen` → `hydrateFromRuns`; applied via `applyPlanData()` in JS), so run-planner changes made while you were viewing are reflected.

**To test:**
1. Open the run planner for a date in one tab + the **finalized** sketch for the same date in another → click **Edit Plan** on the sketch → it should take over **without** a close-tab prompt, and the run-planner tab should show the read-only "Sketch Planner Active" modal within ~10s.
2. With the sketch in **view** mode, make a change on the run planner, **then** click Edit Plan → the sketch should reflect that change.
