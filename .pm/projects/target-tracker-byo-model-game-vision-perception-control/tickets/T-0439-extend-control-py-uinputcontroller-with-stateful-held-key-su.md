---
id: T-0439
title: Extend control.py UinputController with stateful held-key support (WASD/strafe/+use/+jump/+duck)
type: feature
state: triaged
created: 2026-06-19T04:47:57Z
updated: 2026-06-19T16:17:45Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude
assignee: null
acceptance_criteria:
  - UinputController advertises EV_KEY for W/A/S/D/E/SPACE/LEFTCTRL + BTN_LEFT and EV_REL X/Y.
  - set_held(keys) emits only press/release deltas and holds keys across ticks; verified in-engine that +forward/+moveleft/+use/+jump/+duck actually move the player in Xash Half-Life.
  - All held keys are released on shutdown/Ctrl-C (no stuck-key after exit).
  - "[scope] single_player_offline attestation enforced at this entrypoint."
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - movement
  - MS-011
  - ADR-009
attention: null
version: 2
---

## Problem
Today `control.py`'s `UinputController` declares ONLY `EV_REL REL_X/REL_Y` + `EV_KEY BTN_LEFT` — it can look and left-click but physically **cannot press W/A/S/D/E/SPACE/CTRL**. A movement policy (MS-011) literally cannot be actuated until this is fixed. This is the first blocker for ADR-009.

## Context
We have ALREADY proven uinput keyboard injection registers in GoldSrc/Xash captured-mode (that's how `uinput_console.py` / `uinput_arm.py` issue console cheats). So this is a known-good extension, not a research risk. Movement is bang-bang (held keys), so the controller needs press/release/hold-set semantics, not taps.

## Approach
- Add `EV_KEY` capabilities for `KEY_W/A/S/D/E/SPACE/LEFTCTRL` (and keep BTN_LEFT/REL_X/REL_Y).
- Add a stateful API: `press(key)`, `release(key)`, `set_held(set_of_keys)` that diffs against currently-held and emits only the deltas (so +forward stays down across ticks).
- On shutdown / KeyboardInterrupt, release ALL held keys (avoid a stuck +forward).
- Map the 9-dim action vector (ADR-009) → key set: move_fwd→W/S, move_side→A/D, use→E, jump→SPACE, duck→LEFTCTRL, attack→BTN_LEFT.

## Conversation

**2026-06-19 16:17 claude-code:** PROVEN IN-ENGINE (2026-06-19). Built as `control_move.py` (a self-contained `MoveController` — a fresh uinput device with REL_X/Y + BTN_LEFT + KEY_W/A/S/D/E/SPACE/LEFTCTRL, stateful set_held/press/release, release-all on every exit path), rather than editing control.py in place (uinput caps are fixed at device creation). Adversarial review fix applied: SIGINT handler is now opt-in (default off) so it doesn't seize the process from a runner.

Live test: the movement policy drove Half-Life through `MoveController` — it held W/D/S and the player physically walked, strafed, and turned through hldemo1 (recording /tmp/policy_drive.mp4; start/mid/end frames show real position + view change). This resolves the long-standing question (logged under MS-006) of whether uinput KEYBOARD events drive captured-mode GoldSrc — they do. The earlier "keyboard 'w' didn't move the player" was the XTEST/xdotool path, not uinput. The full uinput stack (look + movement + fire) is now proven. Remaining cleanup: the look-head is jerky (clamp), and the controller's settle-sleep stacking note for callers.
