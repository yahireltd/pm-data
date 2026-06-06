---
id: T-0296
title: Multiplayer — versus & co-op with live voice/text chat (social play)
type: feature
state: triaged
created: 2026-06-06T02:54:15Z
updated: 2026-06-06T02:54:15Z
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
  - Two players can play together in real time — a co-op mode (free the granny together) and a versus mode (compete) — matched by an invite link or code
  - "Players can talk during a game: at minimum live text chat, plus live voice chat"
  - It works across the web and mobile versions, and lets a web player and a phone player play together
  - The networking approach (referee-authoritative vs peer event-exchange) and the voice technology are decided in an ADR before the build, including a clear note on privacy and safety for voice (mute, block, no recording)
  - A hosted real-time backend exists, separate from the simple high-score server
out_of_scope:
  - Large-scale or ranked matchmaking and user accounts — this is invite-a-friend, not a platform
  - "Voice moderation/recording — privacy-first: no recording; basic mute/block only"
  - Anti-cheat beyond the existing demo-level score signing
code_anchors:
  - path: server/src/index.js
  - path: client/src/game.js
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

Play with friends in real time, and make it social:
- **Versus** — race or attack each other (e.g. clearing your wall sends junk at your opponent).
- **Co-op** — team up on one board, two paddles, free the granny together.
- **Live comms** — talk while you play: text chat, and live voice, so it feels like playing with a mate in the room.

## The honest reality (this is a big one, design-first)

- **Needs a live "always-on" server.** Real-time play needs a backend that keeps both players' games in sync moment to moment — very different from the simple high-score server we have now. It has to be built and hosted.
- **Fair real-time action play is genuinely hard.** Over the internet, two devices never quite agree; we have to choose an approach up front (a referee server that's the single source of truth, vs each player running their own board and exchanging events) and handle lag.
- **Voice adds a lot.** Live voice is a separate real-time connection between players (peer-to-peer with a small broker, plus a relay for tricky networks), and brings privacy/safety duties (mute, block, no recording). Text chat is far simpler and can come first.
- **Cross-platform.** It should work across the web and phone versions and let a web player and a phone player play together.

## Decisions to settle first (an ADR)

- Mode rules — what exactly "attack" does in versus; what shared-board co-op looks like.
- Netcode model — referee-authoritative vs peer event-exchange.
- Transport — a realtime server (websockets) for game state + text chat; voice over a peer-to-peer connection (WebRTC) with a connection broker + relay.
- Social — invite a friend by link or code, a simple lobby, who's online.

## Suggested phased plan

- **Phase 1:** 2-player **co-op** on one realtime server, with **text chat** (simplest netcode: shared board, two paddles).
- **Phase 2:** **versus** mode (the attack mechanic) + a lobby / invite-a-friend.
- **Phase 3:** **live voice** + presence and social polish.