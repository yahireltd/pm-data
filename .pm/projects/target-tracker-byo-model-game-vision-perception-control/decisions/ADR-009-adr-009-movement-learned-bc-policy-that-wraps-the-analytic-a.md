---
id: ADR-009
slug: adr-009-movement-learned-bc-policy-that-wraps-the-analytic-a
title: "ADR-009: Movement = learned BC policy that WRAPS the analytic aim path, on a hybrid (boxes + scene-thumbnail) observation"
state: accepted
decided: 2026-06-19
decided_by:
  - austin
  - claude
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets:
  - T-0440
  - T-0441
  - T-0442
  - T-0443
  - T-0444
  - T-0445
version: 1
---

## Context

The current AI player only **aims and fires** (live.py: detector → FPS-angular reticle aim → +attack). To sell a believable "AI player" to game devs (QA bots, playtesting), it must also **move**: strafe, take cover, open doors (+use), jump/duck, navigate. The user explicitly asked to "train movement too." We now have the data source: a human plays Half-Life: Uplink at the box while we capture (frame → human action) pairs (frames via mss + capture; keyboard/mouse via an evdev input logger on the G502 + Magic Keyboard).

This ADR was produced by a multi-agent design+adversarial-verification workflow (manual-play-pipeline). The verifier returned **REWORK** on the naive v1; its fixes are folded into the Decision and Consequences below.

## Decision

For movement **v1, train a behavioral-cloning (BC) policy on the user's own single-player play and run it ALONGSIDE — not in place of — live.py's existing detector + analytic aim path.**

- **Integration (WRAP, don't replace):** aim+fire stay analytic (exact, one-step — relearning aim via BC would be strictly worse and is a selling point). Refactor live.py's per-frame body into ONE perception step (detector.detect → Detection list + 84×84 thumbnail) feeding TWO consumers: (i) the existing aim computation → look_dx/dy + attack; (ii) movement_policy.act(obs) → WASD/use/jump/duck (+ look-intent only when no enemy). An **arbiter** merges: enemy present → analytic aim owns look/attack, policy owns feet; no enemy → policy owns look-intent for navigation.
- **Observation (hybrid):** k-nearest detector boxes (dx,dy normalized by focal per live.py convention; w,h,score,visible) padded/masked + an 84×84 grayscale scene thumbnail. Boxes alone throw away navigation cues (geometry, doors, cover) — the thumbnail restores them. Raw-frame-only was rejected (slower, data-hungry, ignores the detector we already trust).
- **Action space (9-dim GoldSrc vector per control tick):** move_fwd ∈ {-1,0,+1}, move_side ∈ {-1,0,+1} (GoldSrc movement is bang-bang; analog buys little), look_dx, look_dy (continuous mouse counts), attack, use, jump, duck (bools). Strafe/keys are categorical.
- **Method:** BC-first (hybrid encoder → multi-head: softmax move_fwd, softmax move_side, sigmoid attack/use/jump/duck, optional look regression; CE+CE+ΣBCE loss, class-balanced because idle frames dominate). Export movement.onnx like the detectors. DAgger/offline-RL is a **later** refinement, not v1.

## Consequences (incl. verifier rework fixes — these are binding, not optional)

1. **control.py cannot actuate movement yet.** UinputController declares only EV_REL REL_X/REL_Y + BTN_LEFT — no WASD/EV_KEY. **T-0440** must extend it with stateful held-key support before anything else; we have proven uinput keyboard injection works (console cheats), so this is an extension, not a research risk.
2. **God-mode BC bias.** The user currently plays with god mode ON, so demos never show taking cover / breaking engagement → a naive clone learns "walk into fire." v1 navigation + door-use can use god-mode footage, but **cover/survival behavior requires damage-ON demonstrations**; the eval (T-0445) measures survival with **god mode OFF**. Logged as a data-bias risk.
3. **Capture gates must not delete the movement signal.** The detector-ensemble/dedup gates designed for the *detector* dataset (capture2) must NOT be applied blindly to the *action* dataset — pHash dedup and box-agreement would drop exactly the transition frames a movement policy needs. T-0441/T-0442 sample at the control tick and keep idle/transition frames (class-balanced later).
4. **BC compounding error / distribution shift** is real; v1 accepts it and adds a deterministic stuck-recovery reflex (anti-stuck) rather than relying on DAgger/RL (which need a resettable env + nav oracle we don't have yet).
5. **Guardrail:** every new entrypoint (logger, trainer, runner) must enforce the [scope] single_player_offline attestation; a more-complete autonomous player is a higher-capability artifact → RESPONSIBLE_USE + RISK_REGISTER updated. Stays single-player/offline; not for online multiplayer.

## Alternatives considered

- **Replace live.py's aim with a single end-to-end BC policy** — rejected: throws away an exact analytic aim that already works; harder to debug; worse aim.
- **Raw-frame-only observation** — rejected for v1: data-hungry, slower, discards the detector we trust. Revisit if box+thumbnail underperforms.
- **RL from scratch** — rejected for v1: needs a resettable env, reward shaping, and far more compute; BC from human play is the cheap, honest baseline.