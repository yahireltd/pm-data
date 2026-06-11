---
id: T-0353
title: "Consolidate duplicated projects: one Yasite system with surfaces; fold Logistics rollout into Yasystem; rename Yasystem (ADR-039)"
type: chore
state: in_progress
created: 2026-06-10T15:00:58Z
updated: 2026-06-11T23:51:04Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - One yasite project (kind=system) carries all website work with the five surfaces configured
  - T-0346 and T-0347 (and any other website tickets) moved in with correct surface tags, history intact
  - yahire-website and yasite-backend no longer appear in the sidebar/projects list
  - Logistics-route-planning-rollout's 7 tickets (T-0217–T-0223) moved into yasystem with their stream branch pinned on each ticket (T-0352 field); the logistics project retired with a pointer; no ticket ids change
  - Yasystem renamed without 'Main Branch'; its in-flight stream branches (incl. refund-hardening) pinned on tickets
  - No ticket id changes anywhere; all moved tickets open correctly at /tickets/<id>
out_of_scope:
  - Any code changes (this is data work, gated on T-0350/T-0351/T-0352 shipping)
code_anchors:
  - path: web/app/_components/ProjectAssignmentControl.tsx
    symbol: existing move-ticket-between-projects path
  - path: web/app/_actions/projects.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2351
    model: claude-fable-5
    started: 2026-06-11T23:51:04Z
    status: in_progress
labels:
  - taxonomy
  - data-migration
attention: null
version: 5
---

## Problem

Projects are duplicated / misshapen (Austin, 2026-06-11):

- The websites were smeared across three half-empty projects; Austin has now made **P-0015 "Websites (yasite)"** the canonical system (kind=system, repo yahireltd/yasite + branch master, four surfaces configured: yahire-website, chair-hire-london-website, hire-catering-dev, backend) — but **yasite (P-0014, holds nothing after T-0346 moved)** and **yasite-backend (P-0016, T-0347 in review)** still exist as separate initiatives.
- **logistics-route-planning-rollout (P-0007) is really a branch stream of the ERP** (Yasystem): 7 tickets, 6 in review off one commit. A project-per-branch orphans its history when the branch merges (the exact failure ADR-039 exists to prevent).
- The ERP project is named "Yasystem Main Branch", baking a branch into its identity.

## Plan (depends on T-0352 branch-on-ticket being live; T-0351 surfaces is deployed and configured)

1. **P-0015 "Websites (yasite)" is the canonical website system** — already done by Austin (kind, repo, surfaces). Verify the surface keys cover all incoming tickets' areas.
2. Move yasite's (P-0014) and yasite-backend's (P-0016) tickets into P-0015 (ProjectAssignmentControl / actions — note the move CLEARS any surface tag by design), then tag each with the right surface (T-0346 → its site, T-0347 → backend / Site Manager).
3. Retire the two shells P-0014 + P-0016 (delete or mark done with a pointer comment) so the sidebar shows ONE website system.
4. **Fold logistics-route-planning-rollout into yasystem**: move T-0217–T-0223 in, pin their stream branch on each ticket (confirm the branch name with Austin at migration time — the runs reference commit 8b37aef5), then retire the logistics project with a pointer comment. In-review tickets keep their review state and conversations.
5. Rename yasystem: "Yasystem Main Branch" → "Yasystem (ERP)"; kind=system; keep repo + branch=master as the project default; pin in-flight stream branches (e.g. refund-hardening) on their tickets per T-0352.
6. hirecatering stays a surface for now; if/when it becomes a real build-out, spin an initiative project linked via related_projects.

## Caution

This is live-data surgery — do it through the tool (moves preserve ids/history), not by hand-editing files; verify each moved ticket still resolves before deleting shells. Tickets in review keep their attention flags. Moves clear surface tags (T-0351) — re-tag AFTER each move.
