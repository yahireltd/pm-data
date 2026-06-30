---
id: T-0490
title: Scripts (per Conversion Process)
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:18:37Z
updated: 2026-06-30T16:52:24Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 11264
reporter: null
assignee: null
acceptance_criteria:
  - "A reusable script/template library per Conversion Process: System follow-up (email/SMS), Quick close, In-depth needs-assessment, Lifetime post-event follow-up."
  - Scripts are parameterised by segment/level (and by won/lost for Lifetime) so they can be personalised.
  - Usable by BOTH humans and the System nurture bot (T-0494) — same source of truth.
  - Linked to the activities (T-0491) that trigger them; uses agreed terminology.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-terminology.md
    role: conversion processes the scripts map to
relates:
  - T-0485
  - T-0491
  - T-0494
  - T-0496
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

## Problem
We need the actual words/templates per Conversion Process — for reps and for the System nurture bot.

## Design notes
Library keyed by Conversion Process (quote-level) × segment/level (+ won/lost for Lifetime). One source of truth shared
by humans and the automated System follow-up (T-0494). Triggered by activities (T-0491).

## Relates
T-0485 (what to say), T-0491 (activities), T-0494 (nurture bot), T-0496 (playbooks).

## Originally
Filed as *"Scirpts"* (typo) — renamed to **Scripts (per Conversion Process)**; same scope.