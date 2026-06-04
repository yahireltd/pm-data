---
id: T-0196
title: Checkboxes feel slow
type: feature
state: review
priority: p2
created: 2026-06-03T23:51:23Z
updated: 2026-06-04T01:14:36Z
project: pm-tool-self
section: null
parent: null
children: []
order: 54272
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - checkboxes feel more instant
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2352
    model: claude
    started: 2026-06-03T23:52:02Z
    status: completed
    summary: Acceptance-criteria checkboxes are now optimistic (useOptimistic) — they flip instantly and revert on failure.
    ended: 2026-06-04T01:00:49Z
    test_plan: Tick a criterion -> it flips immediately with no lag; rapid clicks track correctly.
labels: []
attention:
  needed_by: agent
  reason: still feels slow to respond and blocks input can we assume done untill we know otherwise?
  since: 2026-06-04T01:14:36Z
---

when i tick a check box i often untick it as it is slow. It seems that it is doing the functionality. before updating the ui cac we update the ui first and then if the functtion fails untick it

## Conversation

**2026-06-04 01:00 claude:** Run run-20260603-2352 completed — Acceptance-criteria checkboxes are now optimistic (useOptimistic) — they flip instantly and revert on failure.
