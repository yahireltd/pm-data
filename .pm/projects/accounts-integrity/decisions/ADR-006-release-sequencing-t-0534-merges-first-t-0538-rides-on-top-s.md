---
id: ADR-006
slug: release-sequencing-t-0534-merges-first-t-0538-rides-on-top-s
title: "Release sequencing: T-0534 merges first; T-0538 rides on top; staged rollout with hypercare"
state: accepted
decided: 2026-07-10
decided_by:
  - Austin
  - claude-code
project: accounts-integrity
supersedes: null
superseded_by: null
tickets:
  - T-0534
  - T-0538
kind: deployment
version: 1
---

## Context
t0538-document-generator-integrity is branched FROM t0534-xero-posting-self-heal's head — 538 contains 534. Both change money paths; Austin releases nothing without staged verification.

## Decision
Order: (1) T-0534 to master → deploy → migration (xero_oauth_tokens) → /xero/login on live → linked-email data fixes → canary posting run → cron 0 6 * * * → re-enable sandbox refresh cron. (2) T-0538 merge after/with it → RBAC route grant for /doc-integrity → params 'xeroOrgShortCode' => '!3DZW9' on live → Austin's 4-part sampling checklist → hypercare: watch invoice-integrity log, validations page (100% PASS expected), digest/run-issues daily. No migrations/cron/config beyond the listed items for T-0538.

## Rollback
Both branches revert cleanly by reverting the merge: T-0538 writes nothing retroactive (forward-only writers, read-only tooling); T-0534's migration/table and healed GUIDs are additive and safe to leave in place on revert. Documents generated while T-0538 was live remain valid (coherent, Xero-accepted) after a revert.

## Consequences
Two release events, each independently verifiable; the historical-cleanup milestone (voids, 251316) can proceed independently of merge timing but must complete before posting runs sweep the affected docs.