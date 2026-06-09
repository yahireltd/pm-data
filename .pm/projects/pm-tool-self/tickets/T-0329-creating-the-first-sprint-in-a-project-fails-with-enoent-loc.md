---
id: T-0329
title: Creating the first sprint in a project fails with ENOENT (lock taken before directory exists)
type: bug
state: triaged
created: 2026-06-09T19:28:10Z
updated: 2026-06-09T19:28:10Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: Claude (yasystem sprint planning)
  channel: mcp
assignee: null
acceptance_criteria:
  - Creating the first sprint in a fresh project succeeds via the MCP without any manual directory creation
  - The parent directory is ensured before the lock file is opened in the shared write path, so all first-entity-in-a-new-directory writes work
  - The CLI and web mirrors of the write path are checked for the same lock-before-mkdir ordering and fixed if affected
  - A regression test covers first-sprint creation in a project with no sprints directory
out_of_scope: []
code_anchors:
  - path: mcp-server/src/lib/io.ts
    symbol: writeMarkdownAtomic
    lines: 70-86
  - path: mcp-server/src/lib/io.ts
    symbol: withLock
    lines: 171-185
  - path: mcp-server/src/tools/sprint-tools.ts
    symbol: runCreateSprint
    lines: 103-167
  - path: cli/src/lib/io.ts
    symbol: writeMarkdownAtomic (mirror)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - mcp
  - dogfood-find
attention: null
version: 1
---

## Problem

Creating a sprint in a project that has never had one fails: pm_create_sprint returns "ENOENT ... sprints/SPR-001-....md.lock". Hit for real while planning the first sprint on the yasystem project — the tool is unusable for every project's first sprint.

## Cause

The atomic markdown writer takes the per-file lock before anything creates the parent directory: writeMarkdownAtomic calls withLock on `<path>.lock` first, and the lock open (O_EXCL) throws ENOENT when the directory is missing. The directory is only created later inside writeTextAtomic, which never runs. Likely affects ANY first entity write into a not-yet-existing directory, not just sprints.

## Secondary issue

The SPR id counter increments even when the write fails — the failed attempts burned SPR-001 and SPR-002 on yasystem, so its first real sprint will be numbered SPR-003. Consider allocating the id only after (or rolling back when) the write fails, or at least documenting that ids are not contiguous.

## Workaround

mkdir the project's sprints directory on the server by hand, e.g. `mkdir -p /opt/pm-tool/data/.pm/projects/<slug>/sprints` — pm_create_sprint succeeds afterwards.