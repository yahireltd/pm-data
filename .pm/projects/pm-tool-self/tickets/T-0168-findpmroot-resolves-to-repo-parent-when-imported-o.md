---
id: T-0168
title: findPmRoot resolves to repo parent when imported outside the CLI/Next runtime
type: bug
state: triaged
priority: p3
created: 2026-06-02T17:06:45Z
updated: 2026-06-03T13:56:07Z
project: pm-tool-self
section: null
parent: null
children: []
order: 39936
reporter: null
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: parked
source: discovered
---

# T-0168: findPmRoot resolves to repo parent when imported standalone

## Problem

When a web _lib/pm action is imported into a standalone bun script (cwd = repo root, no PM_ROOT), findPmRoot() returns the repo PARENT dir instead of the repo, so projectFile points outside .pm and actions fail with 'project not found'. The live Next app and the CLI bin resolve correctly, and PM_ROOT overrides it, so impact is limited to standalone scripts/tests today — but it could bite the hosted/remote MCP server (T-0162) and any scripting. Investigate the walk-up logic in cli/src/lib/paths.ts findPmRoot.

## Acceptance criteria

_To define at sprint promotion._
