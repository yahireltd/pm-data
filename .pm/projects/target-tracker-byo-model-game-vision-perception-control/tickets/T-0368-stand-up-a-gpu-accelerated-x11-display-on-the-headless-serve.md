---
id: T-0368
title: Stand up a GPU-accelerated X11 display on the headless server
type: chore
state: done
created: 2026-06-14T01:15:43Z
updated: 2026-06-19T03:13:32Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] `glxinfo -B` on the chosen DISPLAY shows OpenGL renderer = NVIDIA GeForce RTX 5070 Ti (NOT llvmpipe)"
  - "[x] The display is X11 (not Wayland) and reachable by tools run as austin"
  - "[x] DISPLAY + XAUTHORITY for the working display documented for reuse by later tickets"
out_of_scope:
  - Installing Xash3D (separate ticket)
  - Any Wayland setup
code_anchors:
  - path: control.py
    symbol: XdotoolController
  - path: live.py
    symbol: main
  - path: README.md
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260614-0119
    model: claude-opus-4-8
    started: 2026-06-14T01:19:16Z
    status: completed
    ended: 2026-06-14T01:20:00Z
    summary: "We needed a way for the GPU server to show a game on screen so the tool can \"watch\" it, since the server has no monitor attached. It turned out the server already had everything we needed: it's running a graphical session powered by the NVIDIA RTX 5070 Ti graphics card, and we confirmed the graphics card (not slow software fallback) is doing the drawing. So we can use that existing screen straight away — no extra setup, no reconfiguring the machine. Without this we'd have been stuck rebuilding the server's display from scratch; instead the next steps (installing the game and capturing what's on screen) are unblocked immediately."
    test_plan: |-
      1. SSH in: `ssh austin@192.168.50.19`.
      2. Run: `DISPLAY=:1 glxinfo -B | grep 'OpenGL renderer'` — expect `NVIDIA GeForce RTX 5070 Ti...`, NOT `llvmpipe`.
      3. Confirm it's X11 not Wayland: `loginctl show-session 3 -p Type` → `Type=x11`.
      4. Confirm tools can reach it: `DISPLAY=:1 xdotool getmouselocation` returns a coordinate without error.
      5. Edge case: from a brand-new SSH session, if `DISPLAY=:1` alone fails, re-try with `XAUTHORITY=/run/user/1000/gdm/Xauthority` (documented in README).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Documented the server connection + GPU display (DISPLAY=:1, XAUTHORITY fallback, glxinfo check) in README.md under 'Headless GPU server'.
labels:
  - ms-001
  - infra
  - server
attention: null
version: 10
---

## Problem
The Ubuntu server is headless; we need a GPU-accelerated X11 display the game can render into, that `mss`/`x11grab` can capture and `xdotool` can inject into.

## Context
- RTX 5070 Ti, `nvidia-driver-580-open` (R580 open kernel modules — correct for Blackwell). Xorg installed; GDM runs an Xorg on the GPU (vt2); X socket `:1` exists; `austin` has an active seat0 session on tty2; the SSH session reports `XDG_SESSION_TYPE=tty`. `sudo` needs a password.
- Must be **X11, not Wayland** (Wayland breaks mss capture + xdotool injection) and **GPU-accelerated, not llvmpipe/Xvfb**.

## Approach
1. Install GL/X tooling: `mesa-utils` (glxinfo), `x11-xserver-utils`, `vulkan-tools`, plus `xdotool` + `ffmpeg` for later tickets.
2. Prefer reusing the existing GPU-backed display (e.g. `:1`) with the correct `XAUTHORITY`; otherwise stand up a dedicated GPU Xorg.
3. Verify GPU acceleration with `glxinfo -B`.

## Conversation

**2026-06-14 01:20 claude-code:** Run run-20260614-0119 completed — We needed a way for the GPU server to show a game on screen so the tool can "watch" it, since the server has no monitor attached. It turned out the server already had everything we needed: it's running a graphical session powered by the NVIDIA RTX 5070 Ti graphics card, and we confirmed the graphics card (not slow software fallback) is doing the drawing. So we can use that existing screen straight away — no extra setup, no reconfiguring the machine. Without this we'd have been stuck rebuilding the server's display from scratch; instead the next steps (installing the game and capturing what's on screen) are unblocked immediately.

---

**2026-06-19 03:13 — you**

d
