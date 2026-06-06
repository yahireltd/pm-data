---
id: T-0303
title: "Store-readiness: TestFlight + Play internal-testing checklists"
type: chore
state: ready
created: 2026-06-06T03:35:10Z
updated: 2026-06-06T03:36:06Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - iOS archives to an .ipa and a TestFlight checklist is documented (signing, privacy labels, age rating).
  - Android builds a signed .aab and a Play internal-testing checklist is documented (signing, data-safety form, content rating).
  - Both checklists list exactly what still needs a paid developer account.
  - A docs/mobile-release.md captures the steps.
out_of_scope:
  - Actually publishing — needs paid Apple + Google developer accounts (called out, not done this sprint).
code_anchors:
  - path: docs/mobile-release.md
  - path: ios/App
  - path: android/app/build.gradle
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-3
  - mobile
  - release
attention: null
backlog_status: in_review
estimated_effort: M
source: discovered
---

## Problem
Get both apps to "archive-ready + a documented path to the stores" without needing the paid accounts yet.

## Design notes
Build + sign locally; write the per-store submission checklist, flagging the account-gated steps.