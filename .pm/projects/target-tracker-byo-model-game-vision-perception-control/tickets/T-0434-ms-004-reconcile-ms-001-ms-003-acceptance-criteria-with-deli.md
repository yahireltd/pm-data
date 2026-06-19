---
id: T-0434
title: "MS-004: Reconcile MS-001 + MS-003 acceptance criteria with delivered reality"
type: chore
state: triaged
created: 2026-06-19T00:32:11Z
updated: 2026-06-19T01:33:17Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p0
assignee: null
acceptance_criteria:
  - owner reconciliation strategy chosen (amend vs hold-literal)
  - MS-001 + MS-003 criteria + transparent amendment notes updated accordingly
  - MEMORY.md 'MS-003 CLOSED' note reconciled to the Godot/reticle proxy
  - MS-003 left open until MS-006 evidence exists
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

## Problem
MS-001 criterion "Xash3D runs FREE content" is both UNMET (T-0370 still open) and MIS-WORDED (reality is owned, copyrighted HL: Uplink content; the FWGS engine is open-source, the game data is not "free content"). MS-003 criteria name "Xash3D" + "relative-mouse aim", but the proven loop is Godot + visible-cursor reticle; captured-mode uinput on Xash has zero green evidence (proven only in MS-006). MEMORY.md says "MS-003 CLOSED" which is true only for the Godot/reticle proxy.

## Scope — GATED ON OWNER DECISION (open question)
Owner chooses: (a) AMEND the criteria to match what was built (Godot-reticle proven, Xash-uinput open, owned-not-free content) per ADR-002, or (b) hold them literal and treat true Xash3D + relative-mouse + free-content as remaining build work before close. Then update criteria + amendment notes (each records original wording + why), and reconcile the MEMORY.md note. MS-003 not marked hit until MS-006 proves uinput look+fire on Xash.

Milestone: MS-004. References ADR-002.

## Conversation

**2026-06-19 01:33 claude-code:** Done. Per the owner's decision to amend (rather than hold literal), MS-001 and MS-003 acceptance criteria were reworded to match what was actually built — Godot + visible-cursor reticle is the proven path; the Xash + kernel-input path stays open until MS-006; and the content is owned Half-Life: Uplink data, not "free content". Dated amendment notes were added to each, and the stale "MS-003 CLOSED" note in the working memory was reconciled. (References ADR-002.)
