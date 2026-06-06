---
id: T-0282
title: Sound effects (Web Audio)
type: feature
state: ready
created: 2026-06-06T01:10:13Z
updated: 2026-06-06T01:10:46Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - "Distinct sounds play for: paddle bounce, brick break, power-up caught, life lost, and game over."
  - Audio unlocks on the first user interaction (per browser autoplay rules) and there is a mute toggle.
  - Rapid events are voice-limited / throttled so sound never turns into noise.
  - No audio errors or frame hitches during rapid events.
out_of_scope:
  - Background music (product backlog).
  - Stereo / spatial positioning of sounds.
code_anchors:
  - path: client/src/audio/sfx.ts
    symbol: SfxPlayer
  - path: client/src/audio/sounds.ts
  - path: client/src/game/state.ts
    symbol: GameState
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-2
  - juice
  - audio
attention: null
backlog_status: confirmed_for_release
estimated_effort: S
source: charter
---

## Problem
Sound is the cheapest, biggest boost to game feel after particles. A small set of crisp effects on the key events.

## Context
- Part of milestone M2 · Game feel & scoring (MS-002).
- Web Audio API directly, no library (ADR-001).

## Design notes
- Browser autoplay policy: unlock the AudioContext on first input or sounds stay silent — easy gotcha, call it out in code.
- Voice-limit per sound (cap concurrent instances) so a flurry of brick breaks doesn't clip into noise.
- Mute persists for the session.