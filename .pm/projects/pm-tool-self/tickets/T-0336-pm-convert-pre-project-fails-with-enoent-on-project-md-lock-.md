---
id: T-0336
title: pm_convert_pre_project fails with ENOENT on project.md.lock — project dir never created
type: bug
state: triaged
created: 2026-06-10T01:15:26Z
updated: 2026-06-22T14:04:24Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - pm_convert_pre_project succeeds on a fresh slug with no pre-existing directory
  - PP-006 converts cleanly
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
version: 2
---

## Problem
`pm_convert_pre_project` (MCP) fails every time with:
```
Error: ENOENT: no such file or directory, open '/opt/pm-tool/data/.pm/projects/<slug>/project.md.lock'
```
The convert path appears to acquire a lock on `projects/<slug>/project.md` before creating the project directory, so converting any pre-project into a NEW project is impossible via MCP.

## Repro
1. `pm_create_pre_project` → PP-006 (slug stockpredictionsengine-honest-multi-sleeve-paper-book)
2. `pm_update_pre_project` until `ready: true, blocking: []` (all FORCE gates pass)
3. `pm_convert_pre_project {id: PP-006, slug: stock-predictions-engine}` → ENOENT above
4. Same with default slug and with/without `force` — identical error. Tried 3× (not transient).

## Expected
Convert creates `projects/<slug>/` (mkdir -p) before opening the lockfile, then materialises project.md + milestones.

## Context
Blocks documenting a real project (PP-006 is ready and waiting). Workaround being attempted: create the project directory/web-side first, then re-run convert.

## Conversation

**2026-06-22 14:04 claude-code:** **Backlog triage 2026-06-22 — this looks already FIXED; safe to close.**

Same root cause and same fix as **T-0329 / T-0317**: convert-pre-project now writes through the patched atomic writer with mkdir-before-lock (commit `2592dcc`). Recommend closing as done/duplicate-of T-0317.
