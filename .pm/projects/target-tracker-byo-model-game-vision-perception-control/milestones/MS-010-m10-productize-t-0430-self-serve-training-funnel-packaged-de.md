---
id: MS-010
slug: m10-productize-t-0430-self-serve-training-funnel-packaged-de
title: "M10 — Productize T-0430: self-serve training funnel, packaged deliverable, beachhead + pricing"
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 10240
created: 2026-06-19T00:29:17Z
updated: 2026-06-19T00:29:17Z
acceptance_criteria:
  - The end-to-end user funnel is built (capture-or-upload -> qualify yield+review UI -> held-out train -> download best.onnx + ready-to-run profile + auditable scorecard) with no SSH/CLI; a job queue + per-account data isolation + a documented retention/deletion policy + a security review/threat model + a copyright takedown/abuse path
  - "Guardrail is a structural product feature: signup requires acceptable-use acceptance + legitimate-market intent on 100% of accounts; the deliverable is an offline ONNX + single-player SDK only (no hosted online inference, no video-passthrough); the T-0430 hardware-passthrough request is explicitly declined by the MS-004 ADR; the dual-use residual (a downloaded model is unrestrictable post-download) is named honestly"
  - Every delivered model carries the MS-007 scorecard report; a model failing the ship bar is auto-flagged 'pipeline-proven only' and cannot be delivered as a production guarantee
  - Cost-per-model (GPU-hours on the 5070 Ti) + the single-GPU concurrent-customer ceiling are measured numbers; packaging tiers (free/cheap base, paid per-game specialist, managed enterprise) with price points tied to those economics are defined
  - One beachhead market (accessibility | QA/test-automation | game-AI) is chosen with a written ICP + precise deliverable and validated with real users against a PRE-SPECIFIED go/no-go decision rule (number of users, willingness-to-pay / conversion threshold)
slip_records: []
phase: ship
version: 1
---

# M10 — Productize T-0430: self-serve training funnel, packaged deliverable, beachhead + pricing

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
