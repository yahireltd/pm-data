---
id: T-0440
title: Frame↔action input logger — record synchronized (frame, 9-dim action) from human Uplink play
type: feature
state: triaged
created: 2026-06-19T04:48:07Z
updated: 2026-06-19T04:48:07Z
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
  - Logger resolves devices via /dev/input/by-id symlinks and asserts EV_REL on the mouse node (fails loudly on mislabel).
  - SYN_DROPPED events are recorded as explicit gaps in the log, not silently lost.
  - A session produces a JSONL alignable to capture frames.jsonl by wall-clock; a builder can reconstruct (frame, 9-dim action) pairs within ±1 frame.
  - Header records clock offset + device identity; monotonic+wall-clock on every event.
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
version: 1
---

## Problem
No human-input logger exists in the pipeline: `capture.py` records detector boxes, not the human's actions. To behavioral-clone movement (MS-011) we need synchronized (frame → 9-dim action) pairs.

## Context
A minimal evdev logger already runs (`/tmp/input_logger.py`) capturing the G502 mouse (event4, EV_REL) + Magic Keyboard (event6, EV_KEY) to JSONL with wall-clock + monotonic timestamps. The adversarial review (REWORK) flagged hardening needed: resolve devices by `/dev/input/by-id` (numbers renumber on replug), assert capabilities (mouse node must advertise EV_REL or fail loudly), detect **SYN_DROPPED** (kernel buffer overflow = lost events — must be recorded as a gap, not silently dropped), and a header record with clock-offset + device identity. Read access via `setfacl -m u:austin:r <by-id paths>` (least-privilege, re-granted per session/replug).

## Approach
- Harden the logger per above; emit a session header + per-event rows.
- Align to capture frames by wall-clock (capture2 writes per-frame `time.time()` in frames.jsonl). Build (frame → action) by windowing inputs around each frame's timestamp: mouse look_dx/dy = summed REL over the next control-tick window; keys = held-boolean at frame time.
- Budget ±1 frame for capture latency (screen-grab timestamp ≠ on-screen event time); record the measured offset.
- IMPORTANT (per ADR-009 consequence 3): do NOT apply the detector dataset's pHash-dedup / ensemble-agree gates here — keep idle/transition frames; balance later in T-0442.