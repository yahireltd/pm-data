---
id: T-0369
title: Install Xash3D (open GoldSrc) + free content on the server
type: chore
state: review
created: 2026-06-14T01:15:48Z
updated: 2026-06-14T01:46:50Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - xash3d launches and reaches a menu/map on the GPU display
  - Only free/open content is used (no paid Half-Life assets)
  - The launch command is documented for the capture ticket
out_of_scope:
  - Online/multiplayer of any kind (excluded by project scope)
  - Detector / agent work (later milestones)
code_anchors:
  - path: README.md
  - path: live.py
    symbol: main
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260614-0131
    model: claude-opus-4-8
    started: 2026-06-14T01:31:24Z
    status: completed
    ended: 2026-06-14T01:46:50Z
    summary: "We needed a real game running on the server so the tool has something to \"watch\" and aim at. We installed the lightweight Xash3D (old Half-Life) engine and confirmed it runs on the server's graphics card. But the only FREE Half-Life content is a 1999 Windows installer that Linux can't unpack with normal tools, and the old engine would also need its game code rebuilt for Linux — a multi-step job, not a quick download. Rather than get stuck there, we switched the test game to Godot (a free, open game engine): it installed cleanly, renders on the RTX 5070 Ti, and we captured a live frame of it back to the dev machine. So we now have a real, controllable game on screen — which unblocks the next steps (detecting on-screen targets and steering aim). The Xash3D work isn't wasted; real Half-Life remains an option later if wanted."
    test_plan: |-
      1. SSH in: `ssh austin@192.168.50.19`.
      2. Confirm engine present: `~/xash/xash3d-fwgs-linux-amd64/xash3d` exists (runs, but exits without game content — expected).
      3. Confirm Godot works on GPU: `bash /tmp/run_godot_2d.sh` — log should show 'NVIDIA GeForce RTX 5070 Ti - Compatibility' and write /tmp/godot_frame.png showing 4 red circles on black.
      4. Decision to confirm: OK to use Godot as the test game (vs. real Half-Life) for the detector/loop work? Note recorded in the ticket conversation.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ms-001
  - engine
  - xash3d
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-14T01:46:50Z
version: 6
---

## Problem
We need an actual GoldSrc game to capture. Use **Xash3D** (open-source GoldSrc engine) + freely-licensed content — no Steam/paid Half-Life assets, per project scope.

## Approach
1. Install Xash3D (FleetView) — prebuilt binary or build from source (server has gcc/cmake-installable).
2. Provide a free/open content base (free GoldSrc content / aim map / free mod — no paid HL `valve` assets).
3. Verify it launches and reaches a menu/map on the GPU display from the previous ticket.

## Conversation

**2026-06-14 01:42 claude-code:** **Engine: done. Content: blocked — decision needed.**

The Xash3D game engine is installed and confirmed running on the server's GPU display (`:1`). The remaining blocker is *free content*:

1. The free "Half-Life: Uplink" demo is an old Windows **Wise installer** that standard Linux tools can't unpack (7-zip fails; no other extractor present).
2. Even with the game's asset files, the old engine needs its **game-logic code rebuilt for Linux** (`hlsdk-portable` — no ready-made download).

So "free Half-Life on Linux" is a multi-step build, not a quick download. Three ways forward:
- **A — Godot FPS (already built):** use our existing Godot test game instead. Runs instantly on the GPU, gives the detector the same kind of frames; keep GoldSrc as a later goal. *(Recommended for momentum.)*
- **B — Owned Half-Life:** if austin owns Half-Life on Steam, its Linux build already includes the Linux game code + assets — copy its `valve` folder over and it runs immediately.
- **C — Push the free route:** install a Windows compat layer (Wine) to extract the Wise installer, then build the Linux game libraries. Authentic + free, but several uncertain steps.

Engine install is complete regardless of choice.

**2026-06-14 01:46 claude-code:** Run run-20260614-0131 completed — We needed a real game running on the server so the tool has something to "watch" and aim at. We installed the lightweight Xash3D (old Half-Life) engine and confirmed it runs on the server's graphics card. But the only FREE Half-Life content is a 1999 Windows installer that Linux can't unpack with normal tools, and the old engine would also need its game code rebuilt for Linux — a multi-step job, not a quick download. Rather than get stuck there, we switched the test game to Godot (a free, open game engine): it installed cleanly, renders on the RTX 5070 Ti, and we captured a live frame of it back to the dev machine. So we now have a real, controllable game on screen — which unblocks the next steps (detecting on-screen targets and steering aim). The Xash3D work isn't wasted; real Half-Life remains an option later if wanted.
