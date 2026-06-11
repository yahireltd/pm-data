---
id: T-0351
title: Surfaces on a system project — one project, many sites/areas (ADR-039)
type: feature
state: in_progress
created: 2026-06-10T15:00:01Z
updated: 2026-06-11T22:35:13Z
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
  - Project schema/types carry surfaces (key, name, optional path/repo_url/branch); SCHEMA.md documents it
  - Surfaces editable on the project Overview; ticket detail offers a Surface picker limited to the project's surfaces
  - pm_claim_ticket returns the resolved surface (path + repo + branch) alongside agent_policy; pm_get_ticket shows it
  - Ticket rows/lists surface the chip where project context isn't obvious
  - code_surfaces is reconciled — grown into surfaces or removed, not left as a second parallel field
out_of_scope:
  - Per-surface agent policy (one policy per project stands, ADR-039)
  - Moving existing tickets between projects (migration ticket)
code_anchors:
  - path: schemas/project.schema.json
    symbol: code_surfaces — dormant field to grow or supersede
  - path: schemas/ticket.schema.json
  - path: cli/src/types.ts
  - path: mcp-server/src/tools/claim-ticket.ts
  - path: web/app/projects/[slug]/overview/page.tsx
  - path: web/app/_components/ProjectLabelsEditor.tsx
    symbol: pattern to copy for the surfaces editor
  - path: web/app/tickets/[id]/page.tsx
    symbol: meta fields — add Surface picker
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2220
    model: claude-fable-5
    started: 2026-06-11T22:20:06Z
    status: in_progress
    progress:
      - at: 2026-06-11T22:35:13Z
        note: "Implementation complete: projects can now list their surfaces (the named sites/areas inside one repository), tickets can be tagged with the surface they touch (web picker + agent tool, both validated against the project's list), and when an agent picks up a ticket it's told exactly where the work lives — folder, repository and branch — instead of guessing. Surface chips show on ticket lists. The old dead code_surfaces field is retired. End-to-end tests pass (resolution, inheritance, branch overrides, bad-key rejection, graceful handling of renamed surfaces); both typechecks clean. Adversarial review running before commit."
labels:
  - taxonomy
attention: null
version: 4
---

## Problem

Yasite is ONE repo (/GitHub/yasite) with folders per site — yahirenew (main site), chl (Chair Hire London), frontend (old site), backend (site management), hirecatering (early dev) — but the tool forces one repo+branch per project, so the sites got smeared across three half-empty projects (yahire-website, yasite, yasite-backend). We want one Yasite project where tickets say WHICH site/area they touch, and agents learn the right path from the claim.

## Design (ADR-039)

Project gains `surfaces: [{ key, name, path?, repo_url?, branch? }]` — named sub-areas. Default: a surface inherits the project's repo/branch and adds a path (the Yasite case, folders in one repo); repo_url/branch overrides cover the mixed case later.

- Web: surfaces editor on the project Overview (like ProjectLabelsEditor); ticket detail gets a Surface picker (project's surfaces only).
- Ticket gains optional `surface` (a surface key).
- `pm_claim_ticket` response includes the resolved surface (name, path, repo_url, branch) so the agent works in the right folder without guessing.
- Ticket lists/boards show the surface chip so a glance tells yahirenew work from chl work.
- Note: there's a dormant `code_surfaces: string[]` field (globs) — either grow it into this or supersede it; don't leave both.

## Out of band

The Yasite data consolidation itself is the migration ticket. Branch-on-ticket is its own ticket.