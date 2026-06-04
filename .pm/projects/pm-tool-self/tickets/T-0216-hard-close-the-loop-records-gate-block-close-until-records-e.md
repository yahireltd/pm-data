---
id: T-0216
title: Hard close-the-loop records gate — block close until records exist
type: feature
state: review
created: 2026-06-04T03:05:21Z
updated: 2026-06-04T03:28:50Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - pm_complete_run requires a `records` attestation when status=completed (docs / tech-session / status-note — each either handled+linked or explicitly 'none-needed'); the run cannot complete without it
  - If a tech-session id is supplied it is validated to exist
  - The human close (review→done) blocks 'Approve & mark done' until the reviewer confirms the records checklist — mirrors the existing closing-note gate UX
  - Gate logic lives in ONE shared place consulted by CLI + web + MCP (like requiresClosingNote); it is a hard block, but 'none-needed' is always an allowed answer
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/complete-run.ts
    note: add records attestation
  - path: cli/src/lib/
    note: shared close-the-loop / requiresClosingNote gate
  - path: web/app/_actions/tickets.ts
    note: setTicketState done path
  - path: web/app/_components/ReviewBanner.tsx
    note: human close checklist
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0328
    started: 2026-06-04T03:28:50Z
    status: completed
    ended: 2026-06-04T03:28:50Z
    summary: "Hard close-the-loop records gate: complete_run + CLI + web setTicketState block reaching done until records are attested (docs / tech-session if decisions / status note if phase moved); 'none-needed' allowed per item; one shared gate in state-machine.ts consulted by all surfaces; web RecordsGateProvider checklist wired into ReviewBanner/StateSelector/WhatsNext/Kanban."
    test_plan: "complete_run with no records -> rejected; with records.tech_session=TS-999 -> rejected (unresolved); with valid none-needed/linked records -> succeeds. Web: Approve&done shows the 3-item checklist + blocks until answered; same gate from Kanban drag, StateSelector, WhatsNext."
    records:
      docs: updated
      tech_session: TS-006
      status_note: none-needed
labels:
  - hygiene
  - force-gate
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T03:28:50Z
---

## Problem
AGENTS.md hygiene (docs / tech-sessions / status / milestones) is followed unreliably — agents ship code and the records lag. The T-0215 linter only catches misses after the fact. Per "force-gates bite" (ADR-003), the close-the-loop should HARD-BLOCK until the records exist.

## Decision (confirmed by Austin)
A run/ticket cannot reach `done` until the agent confirms + links the records it should have updated: docs touched, a tech-session if decisions were made, a status note if a phase moved. `none-needed` is always an allowed answer so trivial changes aren't burdened. Extends the close-the-loop machinery (T-0176, ADR-022).

## Conversation

**2026-06-04 03:28 claude-code:** Run run-20260604-0328 completed — Hard close-the-loop records gate: complete_run + CLI + web setTicketState block reaching done until records are attested (docs / tech-session if decisions / status note if phase moved); 'none-needed' allowed per item; one shared gate in state-machine.ts consulted by all surfaces; web RecordsGateProvider checklist wired into ReviewBanner/StateSelector/WhatsNext/Kanban.
