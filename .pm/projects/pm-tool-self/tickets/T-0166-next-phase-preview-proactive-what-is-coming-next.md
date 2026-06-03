---
id: T-0166
title: Next-phase preview (proactive 'what is coming next')
type: feature
state: done
priority: p2
created: 2026-06-02T16:51:57Z
updated: 2026-06-02T17:52:25Z
project: pm-tool-self
section: null
parent: null
children: []
order: 37888
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - The Phase tab shows a Next-phase preview below Advance - the coming phase name, question, and force/guide requirement counts
  - The dashboard ProjectHealthRow shows a one-line next-phase preview (e.g. Next - Build, 2 required)
  - The force-advance dialog shows the target phase requirements (what you are advancing into)
  - web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: cli/src/lib/playbook.ts
  - path: web/app/_lib/health.ts
  - path: web/app/page.tsx
  - path: web/app/projects/[slug]/phase/page.tsx
  - path: web/app/_components/PhaseCommandCenter.tsx
  - path: web/app/_components/ForceAdvanceDialog.tsx
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
# T-0166: Next-phase preview (proactive 'what is coming next')

## Problem

Clarity: nothing previews the NEXT phase. Add a collapsed Next-phase preview (name, question, force/guide counts) below Advance in PhaseCommandCenter, a one-line preview on the dashboard ProjectHealthRow, and the target-phase requirements in ForceAdvanceDialog. Turns reactive surfaces forward-looking so the human sees what they are working toward.

## Acceptance criteria

_To define at sprint promotion._
