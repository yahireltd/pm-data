---
id: T-0454
title: Phase 2 · 2a — Release & integrate the AI customer scoring (v3 honest model → real-time score on the quote)
type: feature
state: triaged
created: 2026-06-22T19:03:37Z
updated: 2026-06-22T19:03:37Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-002
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - The v3 honest conversion model (~0.84 AUC; v2's 0.98 leakage removed) is released from the branch and running in production.
  - A real-time conversion score is written onto each new quote/customer and visible on the relevant screens.
  - The score is exposed as the input signal for segmentation/levels (2b) and routing.
  - Human overrides/disagreements with the score are logged (seeds future model improvement).
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

## Deliverable (2a of the scoring↔segmentation pair)

Release and integrate the AI customer-scoring model that already exists on the **v3 honest branch** (conversion model, ~0.84 AUC — v2's headline 0.98 was confirmed data leakage and removed). This is mostly a **release + integration** job, not new modelling: turn it on and write a **real-time score onto each new quote/customer**. It is the foundation that segmentation (2b) and routing build on — a candidate **early win**, in the Phase-1 "switch on what we've already built" spirit.

## Decision cycle (shared with 2b)

- **SEE:** the conversion score (this ticket) + actual/potential value + history/industry.
- **DECIDE:** the thresholds that map score+value → account level (settled in **2b**).
- **ACT (this ticket):** release the v3 model; write the real-time score onto the quote; expose it as the input for 2b + routing; log human overrides of the score.
- **OUTCOME:** every new quote carries a live, honest score.
- **REVIEW:** model performance reviewed (owner + cadence — see 2b's open decision).

## Links

Feeds **2b — Customer segmentation / levels** (this blocks it).

## Notes

Use **v3-honest**, never v2 (leakage). Code lives in the Ya-Hire-Management repo; P-0018 is code-locked, so the release/integration is done by the human dev, not an agent.