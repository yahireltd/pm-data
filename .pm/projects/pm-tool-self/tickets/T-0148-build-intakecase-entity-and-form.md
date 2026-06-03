---
id: T-0148
title: Build intake/case entity and form
type: feature
state: done
priority: p1
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T18:26:43Z
project: pm-tool-self
section: null
parent: null
children: []
order: 19456
reporter: null
assignee: null
acceptance_criteria:
  - We capture all of the information required by the problem
out_of_scope: []
code_anchors:
  - path: schemas/project.schema.json
  - path: web/app/_components/NewProjectDialog.tsx
  - path: cli/src/commands/new.ts
  - path: .pm/playbook.yml
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: in_review
source: charter
---

# T-0148: Build intake/case entity and form

## Problem

Big-rock #3 (partial). Standalone Intake/Case schema + web form for the pre-project case: problem, proposed solution, value, cost of inaction, headline risks, time estimate, priority rationale. Done: schema, form, CLI load/display, intake-phase gate.

## Acceptance criteria

_To define at sprint promotion._

## Audit note (2026-06-02)

**Audited — narrow scope.** Already built: intake *fields* (project.schema.json), the new-project form (NewProjectDialog.tsx), CLI intake flags, and the intake-phase force gate (playbook.yml). Remaining = a *discrete* Intake/Case entity (intake.schema.json + entity type + CLI/web) for large-project case separation. If phase-gating was the only goal, this is done.

## Closeout (2026-06-02)

Function-complete: intake capture + the Intake force gate are built and in use. The discrete pre-project Case entity is descoped — reopen only if we want a funnel where cases compete before becoming projects.
