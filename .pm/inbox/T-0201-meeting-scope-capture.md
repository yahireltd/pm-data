---
id: T-0201
title: meeting scope capture
type: feature
state: done
priority: p2
created: 2026-06-04T00:44:52Z
updated: 2026-06-04T01:59:22Z
project: null
section: null
parent: null
children: []
order: 9216
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] for a scoping meeting can we have a way to capture suggested features"
  - "[x] input that saves in the meeting view"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0046
    model: claude
    started: 2026-06-04T00:46:07Z
    status: completed
    summary: Scoping meetings can now capture suggested features (add -> grade A/B/C/D -> convert each into a feature ticket), mirroring the outcomes pattern. New optional suggested_features on the meeting + a SuggestedFeaturesEditor on both meeting pages.
    ended: 2026-06-04T01:52:41Z
    test_plan: "Open a meeting -> 'Suggested features' section: add a feature, set a grade, click 'Convert to ticket' -> a feature ticket is created + back-linked (relates), and the suggestion shows its new T-id."
labels: []
attention: null
---

for scope capture meetings, we need a way to capture suggested features then later grade them and convert to feature tickets

## Conversation

**2026-06-04 01:52 claude:** Run run-20260604-0046 completed — Scoping meetings can now capture suggested features (add -> grade A/B/C/D -> convert each into a feature ticket), mirroring the outcomes pattern. New optional suggested_features on the meeting + a SuggestedFeaturesEditor on both meeting pages.

---

**2026-06-04 01:59 — you**

works as described
