---
id: T-0195
title: Dev preview string on login
type: feature
state: in_progress
priority: p2
created: 2026-06-03T23:15:12Z
updated: 2026-06-03T23:16:28Z
project: null
section: null
parent: null
children: []
order: 5120
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - the string is removed
  - we can not do a dev preview at all - there should be no way to do that un less a logged in admin using the impresonate functions
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2316
    model: claude
    started: 2026-06-03T23:16:28Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
---

Dev preview: append `?as=you@company.com` to view a given person’s projects without signing in. i see this on the login screen- i need to be sure that a) it isnt possible to do this, and b)we remove that message
