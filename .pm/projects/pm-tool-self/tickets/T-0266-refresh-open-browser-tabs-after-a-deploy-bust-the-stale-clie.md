---
id: T-0266
title: Refresh open browser tabs after a deploy (bust the stale client bundle)
type: feature
state: triaged
created: 2026-06-05T22:52:44Z
updated: 2026-06-22T16:13:34Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
assignee: null
acceptance_criteria:
  - After a deploy (new build id), already-open authenticated tabs detect the mismatch and show a 'new version available' banner with a Refresh button (prompt, not silent reload).
  - A build-info endpoint exposes the current build id; the client compares it against the id it loaded with.
  - Polling is cheap — checks on tab focus/visibility, not a constant interval; no lingering timers.
  - Login/auth pages (/me, /api/auth/*) are excluded so nothing reloads mid-sign-in.
out_of_scope:
  - Silent auto-refresh without asking (we prompt); reloading the login/auth pages.
code_anchors:
  - path: web/app/api/build-info/route.ts
    symbol: new build-id endpoint
  - path: web/app/_components/BuildRefreshBanner.tsx
    symbol: new banner + poll hook
  - path: web/app/layout.tsx
    symbol: mount the banner (authed shell only)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
collaborators:
  - kind: human
    name: Austin Pickering
version: 1
---

## Problem

A deploy restarts the server but already-open tabs keep the old JS/CSS until a hard refresh — this caused a real false "it’s broken" report today (the pre-fill dialog). A build-id check (poll/SSE) that nudges a refresh fixes it.

## Context

Surfaced dogfooding on 2026-06-05.
