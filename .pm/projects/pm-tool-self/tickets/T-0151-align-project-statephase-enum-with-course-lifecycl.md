---
id: T-0151
title: Align project state/phase enum with course lifecycle
type: chore
state: done
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T17:27:47Z
project: pm-tool-self
section: null
parent: null
children: []
order: 22528
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - At phase=closure with all force gates met, the Phase tab shows a Close project button (closeProject action sets state=done + closed_at); a completion state renders once closed
  - getPhaseHealth exposes ready_to_close; the dashboard surfaces a Ready-to-close signal so the final step is not forgotten
  - An ADR documents the project state-vs-phase model
  - web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: web/app/_components/PhaseCommandCenter.tsx
  - path: web/app/_actions/projects.ts
  - path: web/app/page.tsx
  - path: web/app/projects/[slug]/phase/page.tsx
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

# T-0151: Align project state/phase enum with course lifecycle

## Problem

Big-rock #20 (partial). Reconcile project.state vs project.phase against the course's intake/planning/active/hypercare/live-supported/closed. Decide one lifecycle source of truth.

## Acceptance criteria

_To define at sprint promotion._

## Audit note (2026-06-02)

**Audited — narrow scope.** Both enums (ProjectState, ProjectPhase), the playbook engine and the state machine are implemented. Real remaining work: (a) an ADR documenting the state-vs-phase split, (b) a state/phase-consistency lint rule, (c) fix this ticket's outdated 'active/live-supported/closed' wording. Not a code gap so much as a documentation + guardrail.

## Lifecycle-audit addition (2026-06-02)

Extend scope (audit dead-ends D3/D4): a web **Close project** button in PhaseCommandCenter when phase=closure and gates met -> a closeProject action setting state='done' + closed_at + a completion screen, and a dashboard 'Ready to close' signal (getPhaseHealth.ready_to_close). The lifecycle currently has no obvious END.
