---
id: MS-002
slug: v2-hard-guidance-spine-makeover-foundation
title: "v2: hard-guidance spine + makeover foundation"
project: pm-tool-self
state: hit
order: 2048
created: 2026-05-29T13:27:52Z
updated: 2026-05-29T13:27:52Z
acceptance_criteria:
  - Real force gates across Intake / Build / Ship / Hypercare phases
  - Substance checks (meeting.held_substantive + non-empty milestone acceptance criteria), not mere existence
  - Intake-skip bug fixed (new projects start in Intake)
  - Force-anyway records a permanent scar (Project.phase_overrides)
  - STATUS001 status-cadence lint rule nags on stale status notes
  - UI primitive library + unified StatusPill colour system
  - Re-skinned shared rows (TicketRow / StateBadge)
  - Dollar costs removed throughout (time-not-money)
  - In-app Intake project creation
  - Dashboard phase-health
  - Charter scope / closure / lessons editors
  - Command-center polish
slip_records: []
target_date: 2026-05-29
phase: build
---

# v2: hard-guidance spine + makeover foundation

## Acceptance criteria

_What must be true for this milestone to count as hit._

- Real force gates across Intake / Build / Ship / Hypercare phases.
- Substance checks: `meeting.held_substantive` and non-empty milestone acceptance criteria — the gate inspects content, not just existence.
- Intake-skip bug fixed; new projects start in Intake (`phase: intake`).
- Force-anyway requires a typed reason and records a permanent scar in `Project.phase_overrides`.
- STATUS001 cadence lint rule nags when the weekly status note goes stale.
- UI primitive library plus a unified `StatusPill` colour system.
- Re-skinned shared rows (`TicketRow`, `StateBadge`).
- Dollar costs removed throughout — the tool talks in time, not money.
- In-app Intake project creation (web form produces the same shape as `pm new project`).
- Dashboard phase-health surface.
- Charter scope / closure / lessons editors.
- Command-center polish.

## Notes

W1 of the v2 push. Shipped end-to-end: the hard-guidance spine (force gates + substance
checks + force-anyway scar + STATUS001) and the makeover foundation. All workspaces typecheck
and `next build` passes.
