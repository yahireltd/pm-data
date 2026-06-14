---
id: PP-007
slug: target-tracker-byo-model-game-vision-perception-control
title: Target Tracker — BYO-model game-vision perception & control
state: converted
created: 2026-06-14T01:11:17Z
updated: 2026-06-14T01:12:33Z
stakeholders: []
source_tickets: []
summary: |-
  A single-player/offline computer-vision toolkit: capture a game's screen, detect targets with a user-supplied model, and drive input — generalizing the existing target-tracker pipeline (capture → detect → agent/decide → control).

  What exists today: HSV color-mask detector (tracker.py); a TrackingAgent that selects/leads/fires (agent.py); a pygame test world with optional cover/visibility (world.py) + a headless eval harness with metrics and contact-sheet/GIF/MP4 renders (harness.py, visualize.py, make_gif.py, make_video.py); Linux/X11 cursor control via xdotool (control.py); and a closed live loop (live.py). A headless Ubuntu 24.04 GPU server (RTX 5070 Ti, nvidia-driver-580-open, 187GB RAM) is available for model inference.

  Direction: make the DETECT step bring-your-own-model (ONNX/YOLO first, run on the GPU), make capture/control swappable, and target real engines starting with Xash3D (open-source GoldSrc) using free content. Possible later: USB-HID controller emulation for offline console automation.

  SCOPE GUARDRAIL (hard constraint): single-player / offline use only — game-AI research, QA/test automation, accessibility, and CV/dataset tooling. Explicitly NOT for online multiplayer, and no anti-cheat-evasion features.
owner:
  kind: human
  name: austin
version: 3
problems:
  - Perception is a hardcoded HSV color-mask (tracker.py) that only finds bright solid-color blobs — it can't see textured enemies in real games.
  - "Detection is not pluggable: there is no clean way to drop in a user-supplied (ONNX/trained) model."
  - Capture and input-control are Linux/X11-specific and not abstracted; there is no per-game configuration/profile system.
  - The pipeline has only ever run against the pygame test world — never validated end-to-end against a real game engine.
goals:
  - Make the DETECT step bring-your-own-model (ONNX-first), runnable on the RTX 5070 Ti.
  - Run the full capture -> detect -> decide -> control loop against a real engine (Xash3D / GoldSrc) using free content.
  - Keep capture and control swappable and config-driven per game.
  - Preserve the headless test-world harness for fast agent development.
scope_in:
  - Pluggable Detector interface + standard detection schema (boxes/classes/scores).
  - Built-in OnnxDetector running a YOLO .onnx on the GPU, replacing/augmenting the HSV mask.
  - Xash3D (open GoldSrc) on the Ubuntu GPU server with free content; X11 capture + xdotool/uinput control.
  - Per-game config/profiles (region, model, class->action, sensitivity, fire rules).
  - Headless eval harness + visual debug (frames / GIF / MP4).
  - "Single-player / offline targets only: game-AI research, QA/test automation, accessibility, CV/dataset tooling."
  - "Optional later: USB-HID controller emulation for OFFLINE console automation."
scope_out:
  - Online / multiplayer use of any kind — explicitly excluded and enforced (target offline/bot/campaign modes only).
  - Anti-cheat evasion, stealth, or detection-avoidance features of any kind.
  - A polished consumer GUI app (possible far-future, not now).
  - Cloud / remote inference service (the local GPU server is sufficient).
success_criteria:
  - A user can supply an .onnx model + a capture region and get detections feeding the agent with no code changes.
  - The live loop drives aim/click against Xash3D on the server, demonstrated via pulled-back debug video.
  - Accuracy / shots-on-hidden-style metrics reproduce deterministically in the headless harness.
  - README clearly documents the single-player/offline-only scope.
cost_of_inaction: "Without a pluggable model interface and a real-engine target, the project stays a closed pygame demo: it can't serve as a general single-player game-AI / computer-vision testbed, and the available RTX 5070 Ti GPU server sits idle."
milestones:
  - title: M1 — GPU display + Xash3D running
    acceptance_criteria:
      - GPU-accelerated X display verified (glxinfo shows RTX 5070 Ti, not llvmpipe)
      - Xash3D runs free content windowed at a fixed capture region
      - A real game frame is captured and pulled back to the dev machine
  - title: M2 — Pluggable ONNX detector
    acceptance_criteria:
      - Detector ABC + Detection dataclass defined
      - OnnxDetector runs a YOLO .onnx on the GPU and returns boxes
      - live.py uses the detector via config instead of hardcoded find_target
      - Detections visualized on captured frames
  - title: M3 — Closed live loop on a real game
    acceptance_criteria:
      - capture -> detect -> agent -> control runs against Xash3D
      - Relative-mouse aim converges onto a detected target
      - Debug video shows the tracking working
      - Single-player/offline guardrail documented in README + config
workstream_ownership:
  - workstream: Engine/infra (GPU server, Xash3D, X11 capture)
    owner: austin
  - workstream: Perception/ML (ONNX detector)
    owner: austin
  - workstream: Agent + control loop
    owner: austin
kickoff_held: true
project: target-tracker-byo-model-game-vision-perception-control
---

# PP-007: Target Tracker — BYO-model game-vision perception & control

## Summary

A single-player/offline computer-vision toolkit: capture a game's screen, detect targets with a user-supplied model, and drive input — generalizing the existing target-tracker pipeline (capture → detect → agent/decide → control).

What exists today: HSV color-mask detector (tracker.py); a TrackingAgent that selects/leads/fires (agent.py); a pygame test world with optional cover/visibility (world.py) + a headless eval harness with metrics and contact-sheet/GIF/MP4 renders (harness.py, visualize.py, make_gif.py, make_video.py); Linux/X11 cursor control via xdotool (control.py); and a closed live loop (live.py). A headless Ubuntu 24.04 GPU server (RTX 5070 Ti, nvidia-driver-580-open, 187GB RAM) is available for model inference.

Direction: make the DETECT step bring-your-own-model (ONNX/YOLO first, run on the GPU), make capture/control swappable, and target real engines starting with Xash3D (open-source GoldSrc) using free content. Possible later: USB-HID controller emulation for offline console automation.

SCOPE GUARDRAIL (hard constraint): single-player / offline use only — game-AI research, QA/test automation, accessibility, and CV/dataset tooling. Explicitly NOT for online multiplayer, and no anti-cheat-evasion features.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
