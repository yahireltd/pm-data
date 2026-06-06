---
id: T-0280
title: Particle bursts & screen shake on brick break
type: feature
state: ready
created: 2026-06-06T01:09:58Z
updated: 2026-06-06T01:10:46Z
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
  - Breaking a brick emits a short particle burst in the brick's colour.
  - Breaking a brick triggers a brief screen shake that scales up with the current combo.
  - Particles are pooled — no per-frame allocation; 60fps holds even with many simultaneous bursts.
  - A reduced-motion toggle dampens the shake for players who prefer it.
out_of_scope:
  - Sound effects (separate ticket).
  - Background / parallax effects (product backlog).
code_anchors:
  - path: client/src/fx/particles.ts
    symbol: ParticlePool
  - path: client/src/fx/screenshake.ts
    symbol: ScreenShake
  - path: client/src/engine/renderer.ts
    symbol: Renderer
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-2
  - juice
attention: null
backlog_status: confirmed_for_release
estimated_effort: M
source: charter
---

## Problem
'Juice' is what turns a correct game into a satisfying one. Breaking a brick should feel impactful — particles plus a little shake.

## Context
- Part of milestone M2 · Game feel & scoring (MS-002).
- The 60fps target (MS-001) must survive heavy effects.

## Design notes
- `ParticlePool` pre-allocates a fixed pool and recycles — never allocate per frame (TS-001 decision; protects 60fps).
- Shake is a decaying offset applied at draw time in the renderer; magnitude scales with combo, clamped.
- Respect reduced-motion (accessibility): a toggle scales shake to near-zero.