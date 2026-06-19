---
id: MS-006
slug: m6-captured-mode-loop-proven-on-xash-in-game-hit-rate-harnes
title: M6 — Captured-mode loop proven on Xash + in-game hit-rate harness (real-engine ground truth)
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 6144
created: 2026-06-19T00:28:43Z
updated: 2026-06-19T00:28:43Z
acceptance_criteria:
  - An xinput trace proves uinput motion arrives as XI_RawMotion (not XTEST) on :1; a calibrated Xash profile (sens/fov/gain, raw-input cvars, accel off) is committed; Xash launched windowed at a fixed region with a real frame pulled back (closes T-0370)
  - live.py --fps --backend uinput drives Xash look so angular error to a detected NPC converges below a numerically-defined fire tolerance within a bounded window (median <=5 loop iterations, oscillation amplitude bounded), with the OS cursor engine-captured (not a visible reticle); debug video of look-convergence + fire; --fps promoted to first-class
  - The engine writes an append-only authoritative hit/kill log (Godot + Xash/HLSDK) WITH a shot<->hit correlation key (fire id + shared monotonic clock) so eval can attribute outcomes; hit-rate read from the engine, never from pixels; oracle validated at >=90% EVENT-LEVEL agreement vs a human-counted clip of defined length
  - eval_live.py runs a SEEDED scenario unattended -> JSON (hit-rate, KPM, time-to-first-hit, wasted-shot rate, median frames-to-converge, static/moving split, FPS); re-runs reproduce within a PRE-COMMITTED variance band
  - An oracle-detector baseline isolates control-only vs detection-attributable hit-rate loss; a focus/grab watchdog lets a >=10-minute headless run complete unattended (auto-recover + log loss); per-stage latency (capture/detect/inject) reported against a budget
slip_records: []
phase: build
version: 1
---

# M6 — Captured-mode loop proven on Xash + in-game hit-rate harness (real-engine ground truth)

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
