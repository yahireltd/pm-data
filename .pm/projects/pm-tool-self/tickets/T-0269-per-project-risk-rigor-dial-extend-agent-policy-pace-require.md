---
id: T-0269
title: "Per-project risk / rigor dial (extend agent-policy: pace + required rigor by risk)"
type: feature
state: triaged
created: 2026-06-05T22:53:29Z
updated: 2026-06-05T22:53:29Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - A per-project risk/rigor level that scales how carefully agents work + how much verification is required
  - Loose/fast on low-risk new tools; strict on production (down to the branch)
  - Builds on agent_policy (T-0247 commit/push) + branch + GitHub branch protection; the dial visibly drives gates / test rigor / agent autonomy
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

The human→agent control plane needs a "how fast vs how careful" dial per project, down to risk (Austin’s words). Extends agent_policy (T-0247) beyond commit/push into pace + required rigor.

## Context

Relates to T-0252 (tighten testing) and T-0247/ADR-033 (agent guardrails). Surfaced dogfooding on 2026-06-05.