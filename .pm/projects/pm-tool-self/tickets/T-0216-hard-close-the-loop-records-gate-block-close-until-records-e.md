---
id: T-0216
title: Hard close-the-loop records gate — block close until records exist
type: feature
state: triaged
created: 2026-06-04T03:05:21Z
updated: 2026-06-04T03:05:21Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
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
agent_runs: []
labels:
  - hygiene
  - force-gate
attention: null
---

## Problem
AGENTS.md hygiene (docs / tech-sessions / status / milestones) is followed unreliably — agents ship code and the records lag. The T-0215 linter only catches misses after the fact. Per "force-gates bite" (ADR-003), the close-the-loop should HARD-BLOCK until the records exist.

## Decision (confirmed by Austin)
A run/ticket cannot reach `done` until the agent confirms + links the records it should have updated: docs touched, a tech-session if decisions were made, a status note if a phase moved. `none-needed` is always an allowed answer so trivial changes aren't burdened. Extends the close-the-loop machinery (T-0176, ADR-022).