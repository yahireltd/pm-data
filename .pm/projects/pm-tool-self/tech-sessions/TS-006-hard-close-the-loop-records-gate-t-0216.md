---
id: TS-006
slug: hard-close-the-loop-records-gate-t-0216
title: Hard close-the-loop records gate (T-0216)
project: pm-tool-self
created: 2026-06-04T03:23:09Z
updated: 2026-06-04T03:23:47Z
decisions:
  - "Attestation shape: records = { docs: \"updated\"|\"none-needed\" (+ optional docs_note), tech_session: \"<TS-id>\"|\"none-needed\", status_note: \"written\"|\"none-needed\" }. Minimal + schema-valid; stored on the agent_run (agent-run.schema.json gets an optional `records` block). \"none-needed\" is always an allowed answer per item so trivial changes aren't burdened."
  - "Single shared gate: added checkRecordsAttestation(records, resolveTechSession?) + requiresRecordsAttestation(from,to) next to requiresClosingNote in cli/src/lib/state-machine.ts (the ONE place CLI + web + MCP already consult). TS-id RESOLUTION is surface-specific, so the gate takes an injected resolver callback: MCP uses findTechSessionPath, CLI/web use findEntityById. The pure gate validates shape; the resolver confirms the TS-id is real."
  - "HARD block at every done-path, not just one: pm_complete_run (status=completed) rejects without records; the human review→done in setTicketState also rejects without records. To stop bypass-by-surface, the gate was wired into ALL ticket→done paths — web ReviewBanner/StateSelector/WhatsNext/KanbanBoard AND cli `pm state` AND cli `pm record-run` (the CLI mirror of complete-run). Agents still never reach done directly; completed lands in review and stores the agent's attestation on the run."
  - "Human-close UX mirrors the closing-note modal: extracted a root-mounted RecordsGateProvider + useRecordsGate() hook (same pattern as TextPromptProvider/useTextPrompt), so every done-path does note → records → close with no duplicated modal. Each checklist item is answered \"Done / linked\" or \"n/a\" (none-needed); a TS-id is required + shape-checked when \"linked\". The provider is UX only — the server gate re-validates, so a forged client can't close the loop."
actions: []
side_quests: []
problem: "AGENTS.md hygiene (docs / tech-sessions / status) was followed unreliably — agents shipped code and the records lagged. T-0216 makes the close-the-loop a HARD block: a run/ticket can't reach `done` until the records it should have updated are attested. Extends T-0176 / ADR-022, doesn't replace them."
success_criterion: A `records` attestation {docs, tech_session, status_note} is REQUIRED to close (each item allows the literal "none-needed"); the decision lives in ONE shared gate fn consulted by CLI + web + MCP; a TS-id must resolve to a real tech-session; the human review→done UX mirrors the existing closing-note modal.
phase: build
---

# TS-006: Hard close-the-loop records gate (T-0216)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
