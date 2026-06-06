---
id: P-0009
slug: demo-neon-smash
name: "DEMO — Neon Smash: arcade brick-breaker with an online leaderboard"
state: planning
phase: build
created: 2026-06-06T00:23:21Z
updated: 2026-06-06T01:21:45Z
owner:
  kind: human
  name: Austin
goal: "A small, self-contained arcade game built to demonstrate how we run a project end-to-end in pm-tool. Players bounce a ball off a paddle to smash a wall of neon bricks, chain combos for a high score, and post that score to a global top-10 leaderboard. The game is just the vehicle — the real deliverable is a worked example of the full human↔agent workflow: charter, milestones, decisions, meetings, sprints, and tightly-scoped tickets, run by the course playbook. Labelled DEMO; not a real product."
success_criteria:
  - A newcomer can open the project and understand the whole pm-tool workflow in under 10 minutes, without a guided tour.
  - Every build-sprint ticket meets Definition of Ready (acceptance criteria + code anchors) and reads as something an agent could pick up cold.
  - "The game is genuinely fun to demo: smooth 60fps, satisfying juice, and a working leaderboard."
  - At least one ticket has travelled the full loop — agent-claimed, completed into review, human-closed — so the handoff is visible in the record.
parent_project: null
related_projects: []
parent_ticket: null
parent_pre_project: PP-003
code_surfaces: []
key_decisions: []
labels: []
order: 1024
problems:
  - We have no single, end-to-end worked example of a project running through pm-tool — from first idea, through planning, into the build sprints. People learn the workflow piecemeal, by osmosis.
  - "When we show pm-tool to someone, we point at half-finished real projects that need a lot of explaining. There's nothing self-contained to click through that shows the whole shape at once: charter, milestones, decisions, meetings, sprints, scoped tickets, and the human-agent handoff."
  - Agents pick up tickets that are under-specified and then over-build or guess. We have no reference set of 'this is what a properly-scoped ticket looks like.'
goals:
  - Produce a self-contained, clickable example project that shows the whole pm-tool workflow end-to-end.
  - Make it concrete and fun — a real, buildable arcade game (Neon Smash) — so the example engages people instead of being abstract.
  - "Set the quality bar for scoping: every build ticket carries acceptance criteria, code anchors, and design notes an agent could act on cold."
  - "Show the human-agent handoff clearly: the agent claims, records progress, and completes into review; a human verifies and closes."
cost_of_inaction: We keep re-explaining the process by hand at every onboarding and every demo. Scoping quality stays uneven, demos stay vague, and the discipline the tool is meant to enforce looks like overhead instead of obviously paying off — which quietly slows adoption of the tool itself. Each instance is a small cost, but it never stops.
scope_in:
  - "A playable single-screen brick-breaker: a paddle you control, a ball that bounces, a wall of bricks to clear, lives, and clear win/lose states."
  - Scoring with a combo multiplier, plus at least two power-ups (e.g. multi-ball and a wider paddle).
  - "'Juice' that makes it feel good: particle bursts when bricks break, a short screen shake, and sound effects on key events."
  - "A global top-10 leaderboard: enter three initials, submit your score, see the table; scores survive a server restart."
  - A title/menu screen, a how-to-play, responsive down to phone width, and a single documented command to run it locally at 60fps.
scope_out:
  - No accounts, logins, or user profiles — three initials only on the leaderboard.
  - No multiplayer and no real-time play against other people.
  - No level editor, no multi-level campaign, and no saving an in-progress game.
  - No monetisation, ads, analytics, or any user tracking.
  - No native mobile app and no app-store packaging — it runs in a browser.
  - No production or cloud hosting — local run only.
  - "No serious anti-cheat: scores are lightly signed, but a determined forger is accepted risk for a demo."
workstream_ownership:
  - workstream: Game client — rendering, physics, juice
    owner: Claude (under Austin's direction)
  - workstream: Leaderboard service & API
    owner: Claude (under Austin's direction)
  - workstream: Product, scope & acceptance
    owner: Austin
  - workstream: Playtest & UAT — voice of the player
    owner: Jordan Lee + Austin
stakeholders:
  - name: Austin
    channel: email
    contact: austin@ya-hire.example
    internal: true
    added_by:
      kind: agent
      name: claude
    added_at: 2026-06-06T00:19:58Z
    role: Sponsor & Product Owner — owns scope and acceptance
    notify_on:
      - state:review
      - outcome_recorded
---

# DEMO — Neon Smash: arcade brick-breaker with an online leaderboard

## Overview

Converted from pre-project PP-003.

## Why now

## Design

## Milestones
