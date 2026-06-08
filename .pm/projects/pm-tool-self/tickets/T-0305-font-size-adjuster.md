---
id: T-0305
title: Font size adjuster
type: feature
state: in_progress
priority: p2
created: 2026-06-08T12:20:56Z
updated: 2026-06-08T14:19:55Z
project: pm-tool-self
section: null
parent: null
children: []
order: 92160
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - We need to set a min fontsize and the other fontsizes be linked to the min font size and scale?
out_of_scope: []
code_anchors:
  - path: web/app/_lib/font-scale.ts
  - path: web/app/_components/FontScaleControl.tsx
  - path: web/app/layout.tsx
  - path: web/app/me/page.tsx
  - path: web/app/globals.css
    lines: 42-46
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1419
    model: claude-opus-4-8
    started: 2026-06-08T14:19:55Z
    status: in_progress
labels: []
attention: null
---

Crete a settings area with a font size adjuster per user
