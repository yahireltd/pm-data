---
id: T-0246
title: Phase dependent default
type: feature
state: in_progress
priority: p2
created: 2026-06-05T19:35:06Z
updated: 2026-06-05T23:49:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 78848
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - the default submenu item of the project is selected according to the description
out_of_scope: []
code_anchors:
  - path: web/app/_lib/project-nav.ts
    symbol: DEFAULT_SECTION_BY_PHASE
  - path: web/app/_lib/project-nav.ts
    symbol: defaultSectionForPhase
  - path: web/app/projects/[slug]/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2349
    model: claude-opus-4-8
    started: 2026-06-05T23:49:41Z
    status: in_progress
labels: []
attention: null
---

In the intake stage of. project default to overview in plan stage default to plan etc
