---
id: T-0156
title: Tech session records entity
type: feature
state: done
priority: p2
created: 2026-05-29T17:57:13Z
updated: 2026-06-02T16:27:19Z
project: pm-tool-self
section: null
parent: null
children: []
order: 27648
reporter: null
assignee: null
acceptance_criteria:
  - Tech-session entity (TS-NNN) - schema + type with problem, success_criterion, decisions[], actions[], side_quests[]
  - CLI pm new tech-session creates one; loaders + findEntityById + dispatch + help wired
  - MCP tools pm_create_tech_session / pm_list_tech_sessions / pm_get_tech_session (agents capture + recall deep-dive context)
  - Web - a Tech sessions tab on the project (list + create); linter validates TS docs (schema + path + detectKind)
out_of_scope: []
code_anchors:
  - path: schemas/tech-session.schema.json
  - path: cli/src/commands/new-tech-session.ts
  - path: mcp-server/src/tools/tech-session-tools.ts
  - path: web/app/_components/TechSessionsList.tsx
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

# T-0156: Tech session records entity

## Problem

Big-rock #27 (missing). tech-session.schema.json (TS-NNN: problem, success criterion, decisions[], actions[], side_quests[]) + CLI + UI. Captures Claude deep-dives as decisions+actions, not transcripts.

## Acceptance criteria

_To define at sprint promotion._
