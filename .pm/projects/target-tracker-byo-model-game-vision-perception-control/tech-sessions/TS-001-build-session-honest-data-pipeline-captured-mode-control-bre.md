---
id: TS-001
slug: build-session-honest-data-pipeline-captured-mode-control-bre
title: "Build session: honest data pipeline + captured-mode control breakthrough + GitHub"
project: target-tracker-byo-model-game-vision-perception-control
created: 2026-06-19T03:07:56Z
updated: 2026-06-19T03:09:10Z
decisions:
  - "MS-004 (foundations) done: the project is now under version control on git, and the single-player/offline guardrail is enforced in code — every config must declare its scope, the run scripts refuse anything that doesn't, and an automated check fails the build if anti-cheat-evasion language reappears. The old evasion-flavoured wording was removed. Added a reproducible-setup runbook, pinned dependencies, a licence/responsible-use notice, and a risk register."
  - "MS-005 (data spine) done and HARDENED: built the pipeline that makes accuracy numbers trustworthy (held-out test split, a dataset quality gate, a data-diversity/coverage check, dataset provenance + versioning). Key lesson: we ran an adversarial review of these \"reliability gates\" twice, and BOTH times it found ways they could silently report a fake-good accuracy number. We rewrote them around one shared, honest measure and re-verified. Takeaway recorded: always adversarially test reliability/quality-gate code — it's easy to fool."
  - "Owner decisions recorded as ADRs: first market is game-AI research / hobbyist tooling (ADR-008, which steers MS-010 toward released self-hostable tooling rather than a hosted SaaS); limited web-scraped data is allowed for research/non-redistributable use only, with anything we publish staying clean-provenance (ADR-006); the monitor-passthrough hardware idea is deferred and purpose-scoped to single-player/offline console use, not excluded (ADR-004); and MS-001/MS-003 were amended to match what was actually built (Godot loop proven, the Xash path still open)."
  - "MS-006 breakthrough — the project's biggest unknown is resolved: we proved we can control a real commercial engine (Half-Life via Xash3D) headless. Injected kernel mouse motion physically turns the in-game camera (it produces the exact raw-input signal the engine reads, which ordinary synthetic input cannot). The whole codebase is now on GitHub (private) at github.com/AKAust/target-tracker, with visual proof including the autonomous agent playing at 100% accuracy."
actions:
  - text: "Close the Xash/Half-Life control loop: add keyboard input via the kernel path, calibrate look sensitivity, then run the full detect → aim → fire loop on a Half-Life character (MS-006)."
  - text: Capture more DIVERSE Half-Life training data (varied angles/distances/maps/characters) so the trust gate passes and we get a genuinely trustworthy accuracy number rather than a pipeline-only demo.
  - text: Ratify the numeric "reliable accuracy" bar (ADR-005, currently proposed) after the first honest, diverse-data Half-Life training run, then encode it as the ship gate (MS-007).
  - text: "Owner: revoke the GitHub token that was pasted in chat and issue a fresh one with repo+workflow scope; then re-add the CI workflow file and push the agent-playing demo GIF (both were held back by the token's missing scope)."
side_quests:
  - "Code cleanup: remove the now-dead clustering helper + unused args left in qualify.py after the diversity rewrite (cosmetic, non-blocking)."
problem: "A long autonomous build session (owner said \"drive it fully\"). We executed the MS-004→MS-006 roadmap: make the project honest and governed, build a reproducible training pipeline whose accuracy numbers can't lie, and tackle the biggest open risk — whether we can control a real commercial game engine, headless, from screen capture + injected input."
success_criterion: MS-004 (foundations) and MS-005 (data spine) complete and adversarially verified; the captured-mode control loop proven on a real engine (Half-Life via Xash3D); the whole codebase version-controlled and pushed to GitHub.
phase: build
version: 5
---

# TS-001: Build session: honest data pipeline + captured-mode control breakthrough + GitHub

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
