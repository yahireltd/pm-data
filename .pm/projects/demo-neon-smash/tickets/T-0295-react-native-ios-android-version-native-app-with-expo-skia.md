---
id: T-0295
title: React Native (iOS + Android) version — native app with Expo + Skia
type: feature
state: triaged
created: 2026-06-06T02:52:39Z
updated: 2026-06-06T02:52:39Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - The game runs on both iOS and Android as a React Native app (testable on a phone via Expo Go), with the same brick-breaker gameplay and the granny-rescue objective
  - Controls work by touch — drag to move the paddle, tap to launch the ball
  - High scores are kept on the device, with the code structured so it can switch to the shared online leaderboard once that server is hosted
  - A short README explains how to run it (install, then start) in a few steps
out_of_scope:
  - App Store / Play Store submission and the developer accounts that requires
  - Hosting the leaderboard server (tracked separately) — v1 uses on-device scores
  - Sharing one game-logic core between the web and mobile versions (a later refactor; v1 ports the logic into the mobile app)
code_anchors:
  - path: mobile/package.json
  - path: mobile/src/engine.js
  - path: mobile/App.js
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## What we want

A version of the game that runs as a real app on iPhone and Android — installable, not just played in a web browser.

## Approach (decided)

Build it with React Native via Expo, drawing the game with Skia (a fast 2D graphics library). We reuse the existing game's logic — how the ball, paddle, bricks, combos and power-ups behave, and the granny-rescue objective — and redraw it natively. Controls become touch and drag; sounds play through the phone. This was chosen over a quick "web page in a wrapper" because we want it to feel like a real native game.

## The honest constraints

- **Leaderboard needs hosting.** A phone can't reach a laptop, so the online top-10 needs the small score server to live at a public web address. The first version keeps high scores on the device and we connect the shared leaderboard once the server is hosted.
- **Store builds are hands-on.** Building and submitting to the App Store / Play Store happens on a Mac with the right developer accounts — outside this tool.
- **Can't be auto-verified here.** Unlike the web version, native app code can't be smoke-tested in our build sandbox, so the first run on a real phone will surface small fixes to iron out.

## Plan

- **v1:** the playable game on iOS + Android via Expo (testable on a phone with Expo Go), with on-device high scores and the granny-rescue gameplay.
- **v2:** connect the hosted leaderboard; app icon + splash; produce store builds.