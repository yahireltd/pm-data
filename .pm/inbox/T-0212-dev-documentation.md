---
id: T-0212
title: Dev documentation
type: feature
state: done
priority: p2
created: 2026-06-04T02:07:55Z
updated: 2026-06-05T12:46:05Z
project: null
section: null
parent: null
children: []
order: 14336
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] A complete dev documentation / wiki for logged-in devs exists: architecture, data model + schemas, the two-repo deploy flow (ADR-011), the MCP tool surface, and conventions"
  - "[x] Surfaced in-app behind login (a /docs or dev-wiki page) and/or a repo doc"
  - "[x] AGENTS.md carries a rule that agents keep the dev doc updated when they change what it documents"
out_of_scope: []
code_anchors:
  - path: web/app/
    note: new logged-in /docs (dev wiki) page
  - path: AGENTS.md
    note: keep-dev-docs-updated rule
  - path: README.md
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0208
    model: claude
    started: 2026-06-04T02:08:38Z
    status: completed
    summary: "In-app dev wiki at /docs behind login (admin/member): 6 sections (overview, architecture, data model+schemas, two-repo deploy, MCP tool surface, conventions) distilled from the real repo docs. Nav link added; AGENTS.md §8 keeps content.ts in lockstep."
    ended: 2026-06-04T03:15:40Z
    test_plan: Visit /docs as admin/member -> the wiki renders all 6 sections. As a stakeholder -> redirected to /me.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

a complete documentation / wiki for logged in devs - to be added as a rule to update on edit

## Conversation

**2026-06-04 03:15 claude:** Run run-20260604-0208 completed — In-app dev wiki at /docs behind login (admin/member): 6 sections (overview, architecture, data model+schemas, two-repo deploy, MCP tool surface, conventions) distilled from the real repo docs. Nav link added; AGENTS.md §8 keeps content.ts in lockstep.

---

**2026-06-05 12:46 — you**

done
