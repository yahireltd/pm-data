---
id: T-0507
title: "Closure gates: real checks + enforced, scar-able project close"
type: feature
state: in_progress
created: 2026-07-02T13:38:35Z
updated: 2026-07-02T13:39:23Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "New playbook checks work on every face: tickets.none_open (no ticket outside done/wontfix/duplicate), milestones.resolved (every milestone hit/missed/cancelled), decisions.none_proposed (no ADR left in proposed)"
  - evaluateProjectPhase supplies tickets and milestone states to the engine (single context builder — CLI, web, health all inherit)
  - closeProject refuses to close past unmet closure FORCE gates; force-close requires a reason and appends a phase_overrides scar
  - Phase command center offers Close anyway (reason required) when closure gates are unmet, mirroring force-advance
  - Unit tests cover the three new checks
out_of_scope: []
code_anchors:
  - path: cli/src/lib/playbook.ts
    symbol: evalCheck / PhaseContext / evaluateProjectPhase
  - path: web/app/_actions/projects.ts
    symbol: closeProject
  - path: web/app/_components/PhaseCommandCenter.tsx
    symbol: close() / ForceAdvanceDialog pattern
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1339
    started: 2026-07-02T13:39:23Z
    status: in_progress
labels:
  - closure
  - playbook
attention: null
version: 3
---

## Problem

Closure is the only lifecycle phase with no execution machinery: the closure record + Close button exist, but you can close a project over open tickets, unresolved milestones, ADRs still in `proposed`, and without a project retro. The course distillation (module 08) specified a closure checklist with each item ticked or skipped-with-reason; it was never built. Server-side, `closeProject` doesn't evaluate the closure gates at all — the UI hides the button, but the action itself is ungated and there is no force-close-with-scar path (ADR-003 pattern).

## Design

- New playbook check keys: `tickets.none_open`, `milestones.resolved`, `decisions.none_proposed`; PhaseContext gains tickets + milestone states so every face evaluates identically.
- `closeProject` enforces the closure phase's unmet FORCE requirements; `force + reason` closes anyway and appends a `phase_overrides` scar (mirrors advancePhase).
- Phase command center gains the force-close path with a required reason.
- The closure checklist itself lives in `.pm/playbook.yml` (data) — rollout is a config change on live, listed in the test plan.
