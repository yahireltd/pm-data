---
id: T-0302
title: "App identity: icons, splash art, names & bundle IDs"
type: chore
state: ready
created: 2026-06-06T03:35:05Z
updated: 2026-06-06T03:36:05Z
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
  - App icons and splash screens are generated for both platforms from one source asset.
  - App display name is 'Smash the Granny Out of It' on both.
  - Bundle/application IDs are set (e.g. com.yahire.smashthegranny) with version + build numbers.
  - The dark theme carries through the splash.
out_of_scope:
  - Marketing screenshots / store listing copy (release ticket).
code_anchors:
  - path: assets/icon.png
  - path: ios/App/App/Assets.xcassets
  - path: android/app/src/main/res
  - path: capacitor.config.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-3
  - mobile
  - assets
attention: null
backlog_status: in_review
estimated_effort: S
source: discovered
---

## Problem
The apps need real identity (icon, splash, name, bundle id) before they can be installed or submitted.

## Design notes
Use `@capacitor/assets` to generate the icon + splash sets from a single 1024px source.