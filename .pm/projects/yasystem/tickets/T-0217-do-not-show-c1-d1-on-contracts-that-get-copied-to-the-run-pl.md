---
id: T-0217
title: Do not show c1 / d1 on contracts that get copied to the run planner
type: feature
state: done
priority: p2
created: 2026-06-04T14:04:01Z
updated: 2026-06-15T15:58:01Z
project: yasystem
section: null
parent: null
children: []
order: 14336
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] When we copy form sketch planner to run planner - do not enter a type count if the job is not split."
  - "[x] we should not see c1 / d1 on not split jobs"
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1405
    model: claude
    started: 2026-06-04T14:05:13Z
    status: in_progress
    summary: Claimed via web UI
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
branch: PickingSketchSalesDashFriday
version: 9
---

## Problem

_Suggested feature from meeting M-003._

Do not show c1 / d1 on contracts that get copied to the run planner

## Conversation

**2026-06-04 18:02 claude-code:** **Done** — committed `8b37aef5` on branch `PickingSketchSalesDashFriday` (not yet merged to master).

`typeCount` is now left **NULL** for non-split jobs in both copy paths (`CopyPlanToRunService::createYaRunContract`, `SketchPlanService::finalize`). The run planner renders `(C1)/(D1)` whenever `typeCount` is non-null, so this removes the stray label.

**To test:**
1. Finalize a sketch containing a **non-split** contract → on the run planner that contract should show **no** `(C1)/(D1)` suffix.
2. Finalize a sketch with a **split** contract (delivery split across 2 vehicles) → those pieces **should still** show `(D1)/(D2)`.

**2026-06-12 01:17 claude-code:** This ticket and its 6 siblings (T-0218–T-0223) moved from logistics-route-planning-rollout into Yasystem with their working branch pinned (PickingSketchSalesDashFriday) — project consolidation T-0353. The route-planning stream now lives inside Yasystem instead of a separate project.

---

**2026-06-15 15:58 — you**

This is working fine
