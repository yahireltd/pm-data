---
id: T-0165
title: Milestone creation web form (unblocks Planning to Build)
type: feature
state: done
priority: p1
created: 2026-06-02T16:51:57Z
updated: 2026-06-02T17:06:45Z
project: pm-tool-self
section: null
parent: null
children: []
order: 36864
reporter: null
assignee: null
acceptance_criteria:
  - A Milestones tab on the project - create a milestone with title + at least one acceptance criterion (the Planning gates), optional target date/phase
  - createMilestone action mirrors pm new milestone; add-criterion, set-state, remove actions
  - The Planning milestones gate fix-it deep-links to the Milestones tab
  - web tsc clean; route renders; lint 0 errors; a milestone with a criterion clears milestones.exist + milestones.have_criteria
out_of_scope: []
code_anchors:
  - path: web/app/_actions/milestones.ts
  - path: web/app/_components/MilestonesList.tsx
  - path: web/app/projects/[slug]/milestones/page.tsx
  - path: web/app/_components/PhaseCommandCenter.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: discovered
---
# T-0165: Milestone creation web form (unblocks Planning to Build)

## Problem

Lifecycle dead-end D2: creating a milestone + acceptance criteria is a Planning FORCE gate with no web form - it is CLI-only (pm new milestone), so a non-dev human cannot advance Planning->Build. Build a MilestoneForm (modal) reachable from board/time-plan + a create action mirroring pm new milestone, and link it from the Planning gate fix-it. Done: form (title req, >=1 acceptance criterion), action, fix-it link.

## Acceptance criteria

_To define at sprint promotion._
