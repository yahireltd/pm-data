---
id: T-0431
title: "MS-004: Initialize git repo + test/CI entrypoint substrate (prerequisite for all gates)"
type: chore
state: triaged
created: 2026-06-19T00:31:44Z
updated: 2026-06-19T00:31:44Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p0
assignee: null
acceptance_criteria:
  - repo is a git repo with a .gitignore excluding venvs/weights/datasets/runs/tmp
  - a single documented test entrypoint runs (pytest or Make)
  - a CI or pre-commit hook host exists that gate scripts plug into
  - branch/PR policy documented; commit-SHA mechanism that MS-005 manifests reference is defined
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
version: 1
---

## Problem
The repo is NOT under version control (`git rev-parse` fails) and has no test runner or CI host. Nearly every later milestone silently assumes git (manifests record a commit SHA — MS-005), a CI guard + "test entrypoint" (MS-004), content-addressed builds, and a regression gate (MS-007) — none bootstrappable without this substrate. Caught by the roadmap completeness critic.

## Scope
git init + .gitignore (venv/, runs/, __pycache__/, datasets/, *.onnx/*.pt weights, /tmp artifacts); remote + branch/PR policy; ONE test entrypoint (Makefile target or pytest config); a CI runner or documented local pre-commit/CI hook host that check_guardrail, dataset QA, and the regression gate all attach to.

Milestone: MS-004. Blocks the other MS-004 tickets.