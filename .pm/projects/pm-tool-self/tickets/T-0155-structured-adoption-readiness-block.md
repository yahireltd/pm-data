---
id: T-0155
title: Structured adoption-readiness block
type: feature
state: done
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T18:12:57Z
project: pm-tool-self
section: null
parent: null
children: []
order: 26624
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - project.adoption_readiness exists in schema + types (audience, rollout, training, comms, success_metrics, feedback_channels)
  - The charter has an inline adoption-readiness editor, anchored at #adoption-readiness
  - The Ship phase guides toward a documented adoption plan (project.has:adoption_readiness) with a working fix-it link
  - cli + web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: schemas/project.schema.json
  - path: cli/src/types.ts
  - path: web/app/_actions/projects.ts
  - path: web/app/_components/AdoptionReadinessCard.tsx
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

# T-0155: Structured adoption-readiness block

## Problem

Big-rock #26 (partial). adoption_readiness (audience, rollout, training, comms, success metrics, feedback channels) + a ship-phase gate. Currently only scattered workflow_impact strings.

## Acceptance criteria

_To define at sprint promotion._

## Audit note (2026-06-02)

**Audited — narrow + split.** `workflow_impact` covers *how work changes*, not adoption readiness. Keep this ticket for the adoption block itself (schema + AdoptionBlock type + field validation). Split off a follow-up for the ship-phase adoption gate / playbook enforcement.
