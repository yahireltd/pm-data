---
id: T-0219
title: Forcing the close of the run planner tab is overkill - we should be able to just take over without them closing it  and show the sketch planner active message on the run planner when sketch takes over
type: feature
state: in_progress
priority: p2
created: 2026-06-04T14:04:09Z
updated: 2026-06-15T14:23:57Z
project: yasystem
section: null
parent: null
children: []
order: 16384
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] when trying to edit a sketch and the run planner is open we need to make sure that the run planner for that day is locked. It currently asks us to close the run planner tab - can we just make sure that it gets locked  (sketch planner active warning is fine we shouldnt need to actually close the tab as somone may have left it open on their machine)"
  - "[x] Also when we go to edit a sketch and the run planner has a live plan- we should make sure that the sketch we are editing is the live plan (eg if we had both open (run planner in edit and skecth in view) then somone made some changes on the run planner those changes need to be visible on the sketch planner - perhaps we need to reload the live when going into edit on skecth planner)"
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
  needed_by: agent
  reason: |-
    for this i think we also need a way to take over the sketch planner from run planner if somone is using it and left open for example. (we need to make sure that they need to reload the live plan on the sketch planner again before editing continues
    )
  since: 2026-06-15T13:44:36Z
branch: PickingSketchSalesDashFriday
version: 9
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

**2026-06-15 14:23 claude-code:** **Reverse takeover + "abandoned tab" detection — implemented (branch PickingSketchSalesDashFriday).**

Picking up the follow-up request: be able to take over the sketch planner when someone left it open, and show how long since they last actually did something so it's obvious the tab was abandoned.

**The core problem found:** the old logic only used a 60-second keepalive heartbeat. A tab left open keeps pinging forever, so an abandoned session looked identical to one in active use — nobody could ever reclaim it. Also, the sketch planner wasn't even watching for being taken over.

**What now happens:**

1. **"Last activity" vs "still connected" are now separate.** The browser only reports genuine activity (clicks, typing, scrolling, dragging) — so a session can show "opened 09:15, last activity 09:18, idle for 3h 10m — looks left open" even though the tab is technically still online.

2. **Anyone blocked can take over (your call on scope).** When you try to edit a date someone else holds — whether you're on the run planner or the sketch planner — instead of a dead-end "you can't edit" message, you now see who has it, how long they've been idle, and a **Take over** button. Sketch-still-supersedes-run automatically as before; this adds the reverse and the second-sketch-user case.

3. **The person who gets taken over is handled cleanly.** Their board immediately goes read-only and they get a clear "another planner took over — your changes are no longer being saved" notice. To carry on they click **Reload live plan to resume**, which pulls the current live state before letting them edit again (so they can't keep working off a stale plan).

**Testing (on the test server once pulled):**
- Open the sketch planner on a date and edit; in another browser/user open the run planner for the same date → the run planner shows the idle time + a Take over button → clicking it makes the run planner editable and the sketch user drops to read-only.
- Leave a sketch session idle and confirm the idle timer climbs (last-activity does not advance just from the tab being open).
- Take over, then on the displaced user click "Reload live plan to resume" → they get the current plan and can edit again.

**Note:** there's a new DB column (`last_activity_at` on `planner_editing_sessions`) — the migration needs running on the test server after pulling.

Not committed yet — awaiting the go-ahead since this branch is policy-locked.
