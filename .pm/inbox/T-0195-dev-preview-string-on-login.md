---
id: T-0195
title: Dev preview string on login
type: feature
state: done
priority: p2
created: 2026-06-03T23:15:12Z
updated: 2026-06-04T01:10:53Z
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
  - "[x] the string is removed"
  - "[x] we can not do a dev preview at all - there should be no way to do that un less a logged in admin using the impresonate functions"
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
    status: completed
    summary: Removed the ?as= dev-preview backdoor from /me (security); the admin previewAs remains the only impersonation path.
    ended: 2026-06-04T01:00:49Z
    test_plan: Signed out, /me?as=someone no longer impersonates and the dev-preview message is gone; admin previewAs still works.
labels: []
attention: null
---

Dev preview: append `?as=you@company.com` to view a given person’s projects without signing in. i see this on the login screen- i need to be sure that a) it isnt possible to do this, and b)we remove that message

## Conversation

**2026-06-04 01:00 claude:** Run run-20260603-2316 completed — Removed the ?as= dev-preview backdoor from /me (security); the admin previewAs remains the only impersonation path.

---

**2026-06-04 01:10 — you**

all done
