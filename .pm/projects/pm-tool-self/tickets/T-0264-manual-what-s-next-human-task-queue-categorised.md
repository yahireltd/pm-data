---
id: T-0264
title: Manual "What's next" — human task queue (categorised)
type: feature
state: triaged
created: 2026-06-05T22:43:55Z
updated: 2026-06-05T22:43:55Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - A manual list where a human queues things THEY need to do — separate from the automated dashboard sections
  - Items are categorised by type (e.g. review code, action/decision, test a new feature, review a sprint, ...)
  - Add / tick-off (complete) / reorder items; completed ones drop off or archive
  - "Decision (planning): per-user (each human's own queue) vs a shared team list; and where it surfaces (dashboard panel and/or its own page)"
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

The dashboard is all automated signals (needs-you-now, agents working, ready-to-close, etc.) — great for "what the system thinks needs attention", but there's no place for a human to manually jot down and track the things THEY know they need to do, to stay on track.

## Context

A manual, human-curated "What's next" / to-do queue, with items grouped by type — e.g. **review code**, **action / decision**, **test a new feature**, **review a sprint**. Add, tick off, reorder. Likely a per-user list (each human's own), surfaced as a dashboard panel and/or its own page. Complements (doesn't replace) the automated sections. Worth a short plan: per-user vs shared, storage shape, the category set, and where it lives.