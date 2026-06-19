---
id: T-0433
title: "MS-004: GUARDRAIL.md charter + [scope] profile attestation + check_guardrail CI guard"
type: feature
state: triaged
created: 2026-06-19T00:31:54Z
updated: 2026-06-19T01:33:05Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p0
assignee: null
acceptance_criteria:
  - GUARDRAIL.md exists and is linked from README + docs
  - profile schema has a required [scope] block; live.py + capture.py refuse profiles lacking a valid single_player_offline attestation
  - check_guardrail fails on a reintroduced evasion term OR a profile missing [scope], wired into the test entrypoint
  - docs state the attestation is an intent gate, not network enforcement
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
version: 2
---

## Scope
Create GUARDRAIL.md (single-player/offline ONLY; no online multiplayer; no anti-cheat-evasion; no video-interception/hardware-passthrough; the legitimate framings) linked from README + docs. Extend the TOML profile schema with a required `[scope]` block (mode=single_player_offline, target_kind). live.py + capture.py refuse to run a profile without a valid attestation. Add scripts/check_guardrail (or pytest): greps source/docs for the evasion denylist (allowlisting GUARDRAIL/ADR text) and asserts every profiles/*.toml has a valid `[scope]`. Wire into the test entrypoint. State honestly that the attestation is an INTENT gate, not network enforcement.

Milestone: MS-004. Implements ADR-001. Depends on git substrate + the evasion-excision ticket.

## Conversation

**2026-06-19 01:33 claude-code:** Done (commit f1c7143). Wrote GUARDRAIL.md (linked from the README); every config profile now carries a required single-player/offline scope attestation, and the run scripts refuse any profile without it. A guard script (run by `make check` and in CI) fails the build if evasion wording reappears or a profile is missing its attestation. Verified both ways: it passes when clean and correctly fails on a planted violation and on an unscoped profile.
