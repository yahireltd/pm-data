---
id: T-0126
title: Per-project `code_root` override (schema + linter)
type: feature
state: done
created: 2026-05-05T16:25:17Z
updated: 2026-05-29T15:51:28Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Project frontmatter accepts an optional `code_root` field (relative or absolute path)
  - Linter resolves anchor paths using project-level code_root if set, else config-level, else fallback (parent of .pm/) — documented in SCHEMA.md
  - Linter emits a clear error when a project's code_root doesn't exist on disk
  - JSON Schema in /schemas/ updated for project entity
out_of_scope:
  - CLI command to set code_root — direct frontmatter edit is fine
  - Per-ticket override — overkill, projects are the unit
code_anchors:
  - path: linter/src/lib/collect.ts
    symbol: resolveCodeRoot
  - path: linter/src/rules/anchors.ts
  - path: schemas
  - path: SCHEMA.md
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260505-1748
    model: claude-code
    started: 2026-05-05T17:48:10Z
    status: completed
    summary: "Implemented per-project code_root: project.code_root schema field, linter precedence (project > config > fallback), a clear ERROR when a project's configured code_root is missing, a web 'Code repository path' field, and SCHEMA.md. Verified end-to-end 2026-05-29."
labels:
  - linter
  - schema
  - dogfood
attention: null
---

## Problem

Currently `code_root` is global (set once in `.pm/config.yml`), which assumes one pm-tool installation tracks one codebase. In dogfood we hit the case immediately: the seeded ya-hire tickets need `code_root` pointing at ya-hire, but the new `pm-tool-self` project tracks the pm-tool repo itself.

We worked around this for the activity ticket by setting `code_root: .` globally, which makes the seeded ya-hire tickets lint *worse* — their paths definitely don't resolve against the pm-tool repo.

## Context

Discovered while filing the first `pm-tool-self` ticket (the agent activity feed). The mismatch was a `ls /home/austin/Documents/Ya-hire-Management` → "No such file or directory" on the dogfood machine, which was a Mac.

## Design notes

- Project schema gains `code_root: string | null` — relative resolves against the parent of `.pm/`, absolute is honored as-is.
- Resolution precedence: project.code_root > config.code_root > fallback(dirname(pmRoot)).
- Linter `anchors.ts` accepts a code_root per ticket's owning project, not just the index-level one.
- `resolveCodeRoot` in `linter/src/lib/collect.ts` becomes `resolveCodeRootForProject(slug)` or similar.

## Out of scope

- A CLI command to set code_root. Direct frontmatter edit is fine for now.
- Per-ticket overrides. Project is the right unit.

## Conversation

**2026-05-05 17:18 claude-code:** Filed as follow-up to T-0125 (activity feed). Surfaced naturally during dogfood when austin asked "where is it getting the project codebase from."
