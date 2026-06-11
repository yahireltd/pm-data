---
id: T-0353
title: "Consolidate duplicated projects: one Yasite system with surfaces; fold Logistics rollout into Yasystem; rename Yasystem (ADR-039)"
type: chore
state: triaged
created: 2026-06-10T15:00:58Z
updated: 2026-06-11T22:19:39Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
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
agent_runs: []
labels:
  - taxonomy
  - data-migration
attention: null
version: 2
---

## Problem

Projects are duplicated / misshapen (Austin, 2026-06-11):

- The websites are smeared across three half-empty projects — yahire-website (P-0015, 1 done ticket), yasite (P-0014, 0 tickets), yasite-backend (P-0016, 1 ticket in review) — while the real structure is ONE repo with folders per site.
- **logistics-route-planning-rollout (P-0007) is really a branch stream of the ERP** (Yasystem): 7 tickets, 6 in review off one commit. A project-per-branch orphans its history when the branch merges (the exact failure ADR-039 exists to prevent).
- The ERP project is named "Yasystem Main Branch", baking a branch into its identity.

## Plan (depends on T-0351 surfaces + T-0352 branch-on-ticket being live)

1. Make **yasite** the canonical website project: kind=system, repo_url → the yasite repo, surfaces = yahirenew, chl, frontend, backend, hirecatering (paths = their folders).
2. Move yahire-website's and yasite-backend's tickets into yasite (ProjectAssignmentControl / actions), tagging each with the right surface (T-0346 → its site, T-0347 → backend).
3. Retire the two empty website shells (delete or mark done with a pointer comment) so the sidebar shows ONE website project.
4. **Fold logistics-route-planning-rollout into yasystem**: move T-0217–T-0223 in, pin their stream branch on each ticket (confirm the branch name with Austin at migration time — the run lists commit 8b37aef5), then retire the logistics project with a pointer comment. Its in-review tickets keep their review state and conversations.
5. Rename yasystem: "Yasystem Main Branch" → "Yasystem (ERP)"; kind=system; keep repo + branch=master as the project default; pin in-flight stream branches (e.g. refund-hardening) on their tickets per T-0352.
6. hirecatering stays a surface for now; if/when it becomes a real build-out, spin an initiative project linked via related_projects.

## Caution

This is live-data surgery — do it through the tool (moves preserve ids/history), not by hand-editing files; verify each moved ticket still resolves before deleting shells. Tickets in review keep their attention flags.
