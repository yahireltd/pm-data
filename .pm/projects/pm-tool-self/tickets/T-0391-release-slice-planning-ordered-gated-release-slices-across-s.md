---
id: T-0391
title: "Release / slice planning: ordered, gated release slices across streams"
type: feature
state: triaged
created: 2026-06-16T17:10:34Z
updated: 2026-06-16T17:10:34Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
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
labels:
  - m-009
  - modeling
attention: null
version: 1
---

## Problem
Much of a system change is "ship the existing unreleased work in gated slices" (PP-002's scope is largely this). The tool models deployment ADRs but not an **ordered release plan** with per-slice gating/rollback across surfaces/streams — so the release strategy lives in prose, not structure.

## Proposal
- A release/slice plan on a project: ordered slices, each with its own gating + rollback + status, spanning surfaces/streams.

## Context
M-009. PP-002 milestone "Unreleased-work release strategy executed (slice 1 live)" is exactly this shape.

## Acceptance criteria
- A project can define an ordered set of release slices.
- Each slice carries gating + rollback + a status.
- Works across a project's surfaces/streams.