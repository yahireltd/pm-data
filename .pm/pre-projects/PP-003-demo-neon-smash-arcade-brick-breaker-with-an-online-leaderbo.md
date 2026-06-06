---
id: PP-003
slug: demo-neon-smash-arcade-brick-breaker-with-an-online-leaderbo
title: "DEMO — Neon Smash: arcade brick-breaker with an online leaderboard"
state: converted
created: 2026-06-06T00:17:36Z
updated: 2026-06-06T00:23:21Z
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
  - name: Sam Patel
    channel: email
    contact: sam@ya-hire.example
    internal: true
    added_by:
      kind: agent
      name: claude
    added_at: 2026-06-06T00:19:58Z
    role: Engineering — dev colleague, reviews agent work
    notify_on:
      - assigned
      - state_change
  - name: Jordan Lee
    channel: email
    contact: jordan@example.com
    internal: false
    added_by:
      kind: agent
      name: claude
    added_at: 2026-06-06T00:19:58Z
    role: Playtester — voice of the player (external)
    notify_on:
      - meeting_held
      - state:done
source_tickets: []
summary: "A small, self-contained arcade game built to demonstrate how we run a project end-to-end in pm-tool. Players bounce a ball off a paddle to smash a wall of neon bricks, chain combos for a high score, and post that score to a global top-10 leaderboard. The game is just the vehicle — the real deliverable is a worked example of the full human↔agent workflow: charter, milestones, decisions, meetings, sprints, and tightly-scoped tickets, run by the course playbook. Labelled DEMO; not a real product."
owner:
  kind: human
  name: Austin
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
success_criteria:
  - A newcomer can open the project and understand the whole pm-tool workflow in under 10 minutes, without a guided tour.
  - Every build-sprint ticket meets Definition of Ready (acceptance criteria + code anchors) and reads as something an agent could pick up cold.
  - "The game is genuinely fun to demo: smooth 60fps, satisfying juice, and a working leaderboard."
  - At least one ticket has travelled the full loop — agent-claimed, completed into review, human-closed — so the handoff is visible in the record.
milestones:
  - title: M1 · Playable core loop
    acceptance_criteria:
      - The ball bounces correctly off the paddle, the walls, and the bricks.
      - Missing the ball costs a life; reaching zero lives ends the game.
      - Clearing every brick wins the game.
      - The game holds a steady 60fps on a typical laptop.
    target_date: 2026-06-19
  - title: M2 · Game feel & scoring
    acceptance_criteria:
      - Hitting bricks in a row without losing the ball builds a visible combo multiplier.
      - At least two power-ups drop and work (e.g. multi-ball and a wider paddle).
      - Breaking a brick produces particles and a brief screen shake.
      - Key events (bounce, break, power-up, life lost) play sound effects.
    target_date: 2026-07-03
  - title: M3 · Online leaderboard
    acceptance_criteria:
      - After game over, the player can enter three initials and submit their score.
      - The top-10 table is shown and updates immediately after a submit.
      - Scores persist across a restart of the leaderboard service.
      - If the leaderboard is unreachable, the game still plays and shows a friendly 'offline' message.
    target_date: 2026-07-03
  - title: "M4 · Ship: one-command local deploy & polish"
    acceptance_criteria:
      - A single documented command builds and serves the game locally.
      - Title screen, how-to-play, and game-over screen are all present.
      - It's playable on a phone-width screen.
      - The README explains clearly how to run it.
    target_date: 2026-07-10
workstream_ownership:
  - workstream: Game client — rendering, physics, juice
    owner: Claude (under Austin's direction)
  - workstream: Leaderboard service & API
    owner: Claude (under Austin's direction)
  - workstream: Product, scope & acceptance
    owner: Austin
  - workstream: Playtest & UAT — voice of the player
    owner: Jordan Lee + Austin
kickoff_held: true
scoping_held: true
project: demo-neon-smash
---

# PP-003: DEMO — Neon Smash: arcade brick-breaker with an online leaderboard

## Summary

A small, self-contained arcade game built to demonstrate how we run a project end-to-end in pm-tool. Players bounce a ball off a paddle to smash a wall of neon bricks, chain combos for a high score, and post that score to a global top-10 leaderboard. The game is just the vehicle — the real deliverable is a worked example of the full human↔agent workflow: charter, milestones, decisions, meetings, sprints, and tightly-scoped tickets, run by the course playbook. Labelled DEMO; not a real product.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
