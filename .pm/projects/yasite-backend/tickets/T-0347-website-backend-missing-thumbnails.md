---
id: T-0347
title: website backend missing thumbnails
type: feature
state: in_progress
priority: p2
created: 2026-06-10T11:08:11Z
updated: 2026-06-10T11:09:05Z
project: yasite-backend
section: null
parent: null
children: []
order: 1024
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260610-1109
    model: claude
    started: 2026-06-10T11:09:05Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
version: 4
---

On yasite backend the thumbnail are missing for chl, as only the webp is displayed, but chl doesn't have webp, just a few, so it should be the jpg/png first then fallback to webp or if webp not found use the jpg/png, yahire should use webp and fallback to jpg/png
