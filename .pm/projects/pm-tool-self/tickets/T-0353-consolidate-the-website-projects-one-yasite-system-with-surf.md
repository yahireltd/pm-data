---
id: T-0353
title: "Consolidate the website projects: one Yasite system with surfaces; rename Yasystem (ADR-039)"
type: chore
state: triaged
created: 2026-06-10T15:00:58Z
updated: 2026-06-10T15:00:58Z
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
  - Yasystem renamed without 'Main Branch'; its in-flight stream branches pinned on tickets
  - No ticket id changes; all moved tickets open correctly at /tickets/<id>
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
version: 1
---

## Problem

The websites are smeared across three half-empty projects — yahire-website (P-0015, planning, 0 tickets), yasite (P-0014, promoted from T-0346), yasite-backend (P-0016, promoted from T-0347) — while the real structure is one repo with folders per site. And the ERP project is named "Yasystem Main Branch", baking a branch into its identity.

## Plan (depends on T-0350 kind + T-0351 surfaces being live)

1. Make **yasite** the canonical project: kind=system, repo_url → the yasite repo, surfaces = yahirenew, chl, frontend, backend, hirecatering (paths = their folders).
2. Move yahire-website's and yasite-backend's tickets into yasite (ProjectAssignmentControl / actions), tagging each with the right surface (T-0346 → its site, T-0347 → backend).
3. Retire the two empty shells (delete or mark done with a pointer comment) so the sidebar shows ONE website project.
4. Rename yasystem: "Yasystem Main Branch" → "Yasystem (ERP)"; kind=system; keep repo + branch=master as the project default; pin in-flight stream branches (pickingsalesdashfriday, refund-hardeningt0331) on their tickets per T-0352.
5. hirecatering stays a surface for now; if/when it becomes a real build-out, spin an initiative project linked via related_projects.

## Caution

This is live-data surgery — do it through the tool (moves preserve ids/history), not by hand-editing files; verify each moved ticket still resolves before deleting shells.