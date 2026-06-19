---
id: ADR-006
slug: data-sourcing-licensing-provenance-and-dataset-versioning-po
title: Data sourcing, licensing, provenance, and dataset-versioning policy
state: proposed
decided: 2026-06-19
decided_by: []
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
The hosted-service direction makes datasets and weights DISTRIBUTABLE artifacts whose provenance determines legal exposure. The preference for capture/licensed/synthetic over scraping is currently aspirational, not enforced.

## Decision (PROPOSED — owner sets the risk posture)
Source tiers: self-captured = a user's own specialist only; explicitly-licensed / CC / public-domain + synthetic = redistributable BASE; scraped copyrighted art = NOT redistributable, gated. Rule: CLIP domain-match != license clearance. HL/Xash posture: open-source engine, owned game data used locally and NOT redistributed. Versioning: lightweight content-hash + provenance manifest (DVC/git-lfs noted as future). No redistributable artifact is produced without a provenance manifest, and a base build refuses unknown-license sources.

## Consequences
Capturing copyrighted commercial single-player titles for a redistributable base is itself a derivative-work question independent of "scraping" — needs owner decisions on which titles are permitted and whether to invest in genuinely free/public-domain/synthetic content for shippable demos.