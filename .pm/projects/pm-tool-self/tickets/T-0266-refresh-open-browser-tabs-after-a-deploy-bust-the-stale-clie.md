---
id: T-0266
title: Refresh open browser tabs after a deploy (bust the stale client bundle)
type: feature
state: triaged
created: 2026-06-05T22:52:44Z
updated: 2026-06-05T22:52:44Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
assignee: null
acceptance_criteria:
  - After a deploy, already-open tabs detect the new build and prompt / auto-refresh
  - No more "it didn’t work" caused by a stale client bundle
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

A deploy restarts the server but already-open tabs keep the old JS/CSS until a hard refresh — this caused a real false "it’s broken" report today (the pre-fill dialog). A build-id check (poll/SSE) that nudges a refresh fixes it.

## Context

Surfaced dogfooding on 2026-06-05.