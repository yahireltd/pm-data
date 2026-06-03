---
id: T-0157
title: Cross-project conventions document
type: feature
state: done
priority: p3
created: 2026-05-29T17:57:13Z
updated: 2026-06-02T18:20:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 28672
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - .pm/conventions.md exists with a hand-written section + a generated lessons section between markers
  - pm conventions show/sync/add works; sync aggregates systemic lessons_learned across projects (idempotent)
  - Closing a project (web) re-syncs the conventions doc
  - A web /conventions page renders the doc and can add a convention
  - cli + web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: cli/src/lib/conventions.ts
  - path: cli/src/commands/conventions.ts
  - path: cli/src/index.ts
  - path: web/app/_actions/conventions.ts
  - path: web/app/conventions/page.tsx
  - path: web/app/_components/ConventionsControls.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: charter
---

# T-0157: Cross-project conventions document

## Problem

Big-rock #30 (missing). .pm/conventions.md + `pm conventions show/edit`, aggregating systemic lessons_learned at closure.

## Acceptance criteria

_To define at sprint promotion._

## Audit note (2026-06-02)

**Audited — split.** Project-level lesson capture (lessons_learned schema + LessonsEditor + actions) is already built and in use. Keep this ticket for the genuinely missing rollup: `.pm/conventions.md` + closure-time aggregation + a `pm conventions` CLI.
