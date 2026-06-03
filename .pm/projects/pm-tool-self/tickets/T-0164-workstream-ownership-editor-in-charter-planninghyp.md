---
id: T-0164
title: Workstream-ownership editor in Charter (Planning/Hypercare dead-end)
type: feature
state: done
priority: p1
created: 2026-06-02T16:51:57Z
updated: 2026-06-02T16:56:46Z
project: pm-tool-self
section: null
parent: null
children: []
order: 35840
reporter: null
assignee: null
acceptance_criteria:
  - Charter has a Workstream Ownership editor (add / edit / remove who-owns-what entries) mirroring the Risk/Closure editors
  - A project server action persists workstream_ownership changes
  - The Planning workstream_ownership gate fix-it deep-links to the charter editor (no greyed dead note)
  - web tsc clean; charter route renders; lint 0 errors
out_of_scope: []
code_anchors:
  - path: web/app/_components/WorkstreamOwnershipEditor.tsx
  - path: web/app/_actions/projects.ts
  - path: web/app/_components/Charter.tsx
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
# T-0164: Workstream-ownership editor in Charter (Planning/Hypercare dead-end)

## Problem

Lifecycle dead-end D1: the workstream_ownership gate (Planning force + Hypercare guide) has NO in-app editor - it shows a greyed unlinked note and the field is CLI-only. Build a WorkstreamOwnershipEditor in web/app/_components/Charter.tsx (mirror the Risk/Closure editors), persist via a project action, and deep-link the Planning gate fix-it to it. Done: editor add/edit/remove, action persists, destFor links to it, lint+tsc clean.

## Acceptance criteria

_To define at sprint promotion._
