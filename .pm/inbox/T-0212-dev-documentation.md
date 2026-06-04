---
id: T-0212
title: Dev documentation
type: feature
state: in_progress
priority: p2
created: 2026-06-04T02:07:55Z
updated: 2026-06-04T02:46:07Z
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
  - "A complete dev documentation / wiki for logged-in devs exists: architecture, data model + schemas, the two-repo deploy flow (ADR-011), the MCP tool surface, and conventions"
  - Surfaced in-app behind login (a /docs or dev-wiki page) and/or a repo doc
  - AGENTS.md carries a rule that agents keep the dev doc updated when they change what it documents
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
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
---

a complete documentation / wiki for logged in devs - to be added as a rule to update on edit
