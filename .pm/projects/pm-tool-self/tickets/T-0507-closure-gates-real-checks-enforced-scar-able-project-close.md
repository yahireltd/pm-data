---
id: T-0507
title: "Closure gates: real checks + enforced, scar-able project close"
type: feature
state: review
created: 2026-07-02T13:38:35Z
updated: 2026-07-02T13:46:07Z
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
    status: completed
    ended: 2026-07-02T13:46:07Z
    summary: "Closing a project now has real teeth. Before, the end of a project was an honour-system button: you could declare a project done while tickets were still open, milestones were never resolved, and decisions sat undecided — nothing checked, so projects could \"end\" without actually being finished. Now the tool checks the closure requirements when you close: leftover tickets must be finished or moved elsewhere, every milestone must be settled (hit, missed, or cancelled), and no decision can be left hanging. If something isn't done, the close is refused with a plain list of what's missing. You can still close anyway — but only with a written reason, which is stamped permanently on the project record, the same way skipping a phase gate works. This works from the web and for agents alike because the rule lives on the server, not just in the interface. Without this, the \"projects drift and never really end\" problem stays; with it, closure is a checked event. The specific checklist is configurable per install and rolls out as a settings change."
    test_plan: |-
      SETUP (one-time, on live): add the closure-phase requirements to the data repo's .pm/playbook.yml (block provided in the sprint summary/chat) — the new checks are: tickets.none_open, milestones.resolved, decisions.none_proposed, plus the existing meeting.held_substantive:retro / status.recent / closure.recorded. Then deploy code.
      1. Pick a test project in the Closure phase with an OPEN ticket. The Phase view's checklist should show "tickets" unmet; the Close button is absent and a "Close anyway…" link appears. 2. Click Close anyway → dialog lists the unmet items and demands a reason; confirm with a reason → project closes, and the project's record (frontmatter phase_overrides) carries the scar with your reason + email. 3. On another test project, resolve everything (tickets closed, milestones hit/missed/cancelled, no proposed ADRs, retro held, closure record written) → checklist all green, normal Close button works, no scar. 4. Server-side enforcement: with an open ticket, close via a direct action call (or the CLI equivalent) without force → refused with the itemised error. 5. Regression: force-ADVANCE between earlier phases still works and still scars; revert phase unaffected; dashboards (health/ready-to-close) still render — they share the same engine. 6. bun test cli/tests/playbook-closure.test.ts passes (9 tests).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - closure
  - playbook
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T13:46:07Z
version: 4
---

## Problem

Closure is the only lifecycle phase with no execution machinery: the closure record + Close button exist, but you can close a project over open tickets, unresolved milestones, ADRs still in `proposed`, and without a project retro. The course distillation (module 08) specified a closure checklist with each item ticked or skipped-with-reason; it was never built. Server-side, `closeProject` doesn't evaluate the closure gates at all — the UI hides the button, but the action itself is ungated and there is no force-close-with-scar path (ADR-003 pattern).

## Design

- New playbook check keys: `tickets.none_open`, `milestones.resolved`, `decisions.none_proposed`; PhaseContext gains tickets + milestone states so every face evaluates identically.
- `closeProject` enforces the closure phase's unmet FORCE requirements; `force + reason` closes anyway and appends a `phase_overrides` scar (mirrors advancePhase).
- Phase command center gains the force-close path with a required reason.
- The closure checklist itself lives in `.pm/playbook.yml` (data) — rollout is a config change on live, listed in the test plan.

## Conversation

**2026-07-02 13:46 claude-code:** Run run-20260702-1339 completed — Closing a project now has real teeth. Before, the end of a project was an honour-system button: you could declare a project done while tickets were still open, milestones were never resolved, and decisions sat undecided — nothing checked, so projects could "end" without actually being finished. Now the tool checks the closure requirements when you close: leftover tickets must be finished or moved elsewhere, every milestone must be settled (hit, missed, or cancelled), and no decision can be left hanging. If something isn't done, the close is refused with a plain list of what's missing. You can still close anyway — but only with a written reason, which is stamped permanently on the project record, the same way skipping a phase gate works. This works from the web and for agents alike because the rule lives on the server, not just in the interface. Without this, the "projects drift and never really end" problem stays; with it, closure is a checked event. The specific checklist is configurable per install and rolls out as a settings change.
