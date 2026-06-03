---
id: T-0153
title: Add test-plan ADR variant
type: feature
state: done
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T18:02:05Z
project: pm-tool-self
section: null
parent: null
children: []
order: 24576
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A test-plan ADR variant (kind=test-plan) exists end-to-end - schema, types, CLI new, web form/template
  - Scenarios are the mandatory section - an accepted test-plan ADR without scenarios is an ADRVAR error
  - The Test phase previews/guides a structured test-plan ADR (decision.accepted_kind:test-plan)
  - cli + web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: schemas/decision.schema.json
  - path: cli/src/types.ts
  - path: cli/src/commands/new.ts
  - path: linter/src/rules/adr-variant.ts
  - path: web/app/_actions/decisions.ts
  - path: web/app/_components/NewDecisionForm.tsx
  - path: web/app/_components/decision/VariantDetails.tsx
  - path: .pm/playbook.yml
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: parked
source: charter
---

# T-0153: Add test-plan ADR variant

## Problem

Big-rock #16 (missing). decision kind=test-plan with mandatory scope/env/exit-criteria/resources + an ADRVAR lint rule.

## Acceptance criteria

_To define at sprint promotion._
