---
id: ADR-024
slug: review-send-back-has-two-distinct-legal-paths-review-ready-a
title: "Review send-back has two distinct legal paths: review->ready and review->in_progress"
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0194
---

## Context
When a reviewer rejects work in `review`, there are two genuinely different intents: 'this needs rework by the same assigned agent' versus 'put it back in the queue for anyone to pick up'. The original machine modeled only one return edge (and at one point review->ready was illegal, so the send-back button broke). Collapsing both into a single edge forces a wrong default and loses the reviewer's intent.

## Decision
Make both edges legal reviewer-reject paths in the ticket state machine: review->in_progress sends it back to the assigned agent, review->ready returns it to the open queue. The UI exposes both buttons and the transition table (state-machine.ts) sanctions both, so neither surface can hit an illegal-transition error.

## Consequences
The `review` node now has multiple non-terminal back-edges, making the lifecycle less linear and giving reviewers a choice they must make consciously. The benefit: send-back preserves intent (rework-by-owner vs re-queue) instead of forcing one behavior, and the dual paths are enforced at the machine level rather than papered over in the UI.