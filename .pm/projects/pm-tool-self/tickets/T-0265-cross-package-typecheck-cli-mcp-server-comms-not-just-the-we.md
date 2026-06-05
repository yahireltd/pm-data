---
id: T-0265
title: Cross-package typecheck (cli, mcp-server, comms) — not just the web build
type: chore
state: triaged
created: 2026-06-05T22:52:32Z
updated: 2026-06-05T22:52:32Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - A typecheck covers cli/, mcp-server/, comms/ AND web/ — not only the deploy’s web build
  - Runnable locally + in CI / pre-deploy so type errors are caught before push, not on the server
  - pm-deploy fails fast if any package fails typecheck
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
---

## Problem

Today the server web build is the ONLY typecheck and it only covers web/ (+ the cli files it imports). Type errors in cli/, mcp-server/, comms/ surface at runtime or a manual check — two server-side build failures today were exactly this. A cross-package typecheck (local + CI/pre-deploy) tightens the feedback loop a lot.

## Context

Surfaced dogfooding on 2026-06-05.