---
id: T-0432
title: "MS-004: Excise anti-cheat-evasion framing across the whole repo (control.py + LIVE_LOOP.md)"
type: chore
state: triaged
created: 2026-06-19T00:31:52Z
updated: 2026-06-19T00:31:52Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p0
assignee: null
acceptance_criteria:
  - repo-wide grep of the evasion denylist returns zero hits outside GUARDRAIL.md/ADR text
  - control.py + docs/LIVE_LOOP.md reframed to the captured-mode/XI2-raw rationale
  - audit log committed
  - denylist is self-consistent (does not red-flag legitimate XI2-raw engineering notes)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem
control.py's module + UinputController docstrings sell uinput as events "indistinguishable from hardware" that "bypass the XTEST synthetic-event flag some anti-cheat rejects"; docs/LIVE_LOOP.md (~line 78) carries the "Godot reads XInput2 raw; xdotool sends XTEST" note. This directly contradicts the guardrail (ADR-001).

## Scope
Rewrite ALL such text to the legitimate rationale ONLY: captured-mode SDL2/Godot engines read XInput2 raw motion, so uinput is a functional requirement for our own offline test games — zero anti-cheat references. Grep the whole repo for the evasion denylist (anti-cheat, bypass, undetect, indistinguishable, stealth, "XTEST flag") and reframe/remove every hit. Reconcile the denylist's "XTEST" term so honest engineering notes aren't red-flagged (reword or a precise allowlist). Deliver an audit log.

Milestone: MS-004. Implements ADR-001/ADR-002. Depends on git substrate.