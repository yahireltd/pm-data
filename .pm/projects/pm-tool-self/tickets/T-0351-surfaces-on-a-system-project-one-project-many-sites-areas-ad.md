---
id: T-0351
title: Surfaces on a system project — one project, many sites/areas (ADR-039)
type: feature
state: review
created: 2026-06-10T15:00:01Z
updated: 2026-06-11T22:51:40Z
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
    status: completed
    progress:
      - at: 2026-06-11T22:35:13Z
        note: "Implementation complete: projects can now list their surfaces (the named sites/areas inside one repository), tickets can be tagged with the surface they touch (web picker + agent tool, both validated against the project's list), and when an agent picks up a ticket it's told exactly where the work lives — folder, repository and branch — instead of guessing. Surface chips show on ticket lists. The old dead code_surfaces field is retired. End-to-end tests pass (resolution, inheritance, branch overrides, bad-key rejection, graceful handling of renamed surfaces); both typechecks clean. Adversarial review running before commit."
    ended: 2026-06-11T22:51:40Z
    summary: |-
      **What we did**
      Projects can now describe their surfaces — the named sites or areas living inside one repository (like the website repo that holds several sites plus a backend). You list them once on the project's Overview; each ticket can then be tagged with the surface it touches, from a picker that only offers that project's surfaces. When the AI picks up a tagged ticket it's told exactly where the work lives — the folder, the repository and the branch — instead of guessing. A small tag on ticket lists shows the area at a glance.

      **Why we did it**
      The websites were smeared across three half-empty projects because the tool could only say one repo per project. There was no way to keep one website project and still tell work on one site apart from work on another — or to point the AI at the right folder.

      **If we did nothing**
      The duplicated projects would stay (and multiply as more sites appear), ticket history would stay scattered, and agents would keep guessing which folder a piece of work belongs to — occasionally building in the wrong one.

      **The benefit**
      One website project can now hold all the website work, clearly sorted by site. This unlocks the planned tidy-up: merging the three website projects into one, with every ticket tagged to its site. It also lays the second of three rails for the sprint — branches on tickets come next, then the consolidation itself.
    test_plan: |-
      After `pm-deploy` (commit b8534e3 — also restarts the MCP so the new claim/get/update behaviour loads) and a hard refresh:

      **Surfaces editor (project Overview)**
      1. Open Yasite → Overview → new "Surfaces" field under Code repository. Add the five: yahirenew (Main site), chl (Chair Hire London), frontend (Old site), backend (Site management), hirecatering — each with its folder as the path. Each appears as a row with key/path shown.
      2. Try adding a second surface with the same key → clean "already exists" error.
      3. Edit one (pencil on hover), change its name → saves. Remove one → gone. Re-add it.

      **Ticket tagging**
      4. Open a ticket IN a project that now has surfaces → a "Surface" picker appears in the right-hand column (it does NOT appear on projects without surfaces). Pick one → saves; reload persists.
      5. The tickets list (/tickets) and Dev Tickets now show a small tag with the surface key on tagged rows.

      **Move = tag cleared (the review catch)**
      6. Tag a ticket with a surface, then move it to ANOTHER project (Project picker on the ticket) → after the move the surface tag is GONE (keys are project-scoped; before this fix the tag silently carried over and could point at the wrong project's same-named surface).

      **Agent surface (MCP — new session or restart)**
      7. `pm_get_ticket` on a tagged ticket → response includes `surface: {key, name, path, repo_url, branch}` with repo/branch inherited from the project (or the surface's own override).
      8. `pm_update_ticket` with `surface: "nope"` → rejected, listing the valid keys. With `surface: ""` → cleared.
      9. Rename a surface's key on the Overview while a ticket still carries the old key → the ticket's picker shows "(no longer a surface — re-pick)" and `pm_claim_ticket` returns a `surface_warning` rather than failing.

      **Cross-impact**
      10. Project create/promote/convert still work (they no longer write the dead `code_surfaces` field); existing projects with `code_surfaces: []` in their files still open fine.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: SCHEMA.md (project surfaces + ticket surface + code_surfaces superseded), dev wiki tool rows + data model, and the overview/ticket-detail/tickets/dev-tickets help screens — same commit.
labels:
  - taxonomy
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-11T22:51:40Z
version: 5
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

## Conversation

**2026-06-11 22:51 claude-code:** Run run-20260611-2220 completed — **What we did**
Projects can now describe their surfaces — the named sites or areas living inside one repository (like the website repo that holds several sites plus a backend). You list them once on the project's Overview; each ticket can then be tagged with the surface it touches, from a picker that only offers that project's surfaces. When the AI picks up a tagged ticket it's told exactly where the work lives — the folder, the repository and the branch — instead of guessing. A small tag on ticket lists shows the area at a glance.

**Why we did it**
The websites were smeared across three half-empty projects because the tool could only say one repo per project. There was no way to keep one website project and still tell work on one site apart from work on another — or to point the AI at the right folder.

**If we did nothing**
The duplicated projects would stay (and multiply as more sites appear), ticket history would stay scattered, and agents would keep guessing which folder a piece of work belongs to — occasionally building in the wrong one.

**The benefit**
One website project can now hold all the website work, clearly sorted by site. This unlocks the planned tidy-up: merging the three website projects into one, with every ticket tagged to its site. It also lays the second of three rails for the sprint — branches on tickets come next, then the consolidation itself.
