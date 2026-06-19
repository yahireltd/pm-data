---
id: ADR-002
slug: visible-cursor-reticle-vs-captured-mode-relative-uinput-aim-
title: Visible-cursor reticle vs captured-mode relative-uinput aim (the XI2-raw constraint)
state: accepted
decided: 2026-06-19
decided_by: []
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
Mouse-captured SDL2 / GoldSrc / Unity / Unreal engines read XInput2 *raw* motion and ignore xdotool's XTEST synthetic events. This (plus flaky mutter pointer-grab) drove the Godot pivot to a visible-cursor reticle and the LIVE_LOOP "journey" items 2 & 4.

## Decision
The visible-cursor reticle (absolute / xdotool) is the robust default for non-captured engines and the accessibility framing. Captured-mode relative aim (kernel uinput via `live.py --fps`) is the general product capability for real engines and MUST be proven on Xash (MS-006) before MS-003 can honestly close.

## Consequences
Determines whether base-data capture can auto-vary via the agent (needs a working uinput look+fire loop) or must fall back to passive/demo playback. MS-003's amended acceptance criteria reference this split. Pure-evasion framing of uinput is removed (see guardrail ADR).