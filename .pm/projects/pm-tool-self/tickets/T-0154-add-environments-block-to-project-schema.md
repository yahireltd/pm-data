---
id: T-0154
title: Add environments block to project schema
type: feature
state: done
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T18:08:23Z
project: pm-tool-self
section: null
parent: null
children: []
order: 25600
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - project.environments[] exists in schema + types (name, purpose, url, divergences, smoke_tests, readiness)
  - The charter has an inline environments editor (add/edit/remove), anchored at #environments
  - The Ship phase guides toward documented environments (project.has:environments) with a working fix-it link
  - cli + web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: schemas/project.schema.json
  - path: cli/src/types.ts
  - path: web/app/_actions/projects.ts
  - path: web/app/_components/EnvironmentsEditor.tsx
  - path: web/app/_components/Charter.tsx
  - path: .pm/playbook.yml
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: charter
---

# T-0154: Add environments block to project schema

## Problem

Big-rock #17 (missing). project.environments[] (name, purpose, divergences-from-prod, smoke tests, readiness checklist).

## Acceptance criteria

_To define at sprint promotion._
