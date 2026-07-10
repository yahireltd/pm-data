---
id: ADR-001
slug: xero-idempotency-keys-are-token-scoped-recovery-is-log-drive
title: "Xero idempotency keys are token-scoped: recovery is log-driven heal + on-Xero backstop, never key replay"
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
version: 1
---

## Context
Xero documents 24h idempotency keys, but staged re-posts in the Demo Company proved keys are scoped to the ACCESS TOKEN — after any token refresh, replaying a key creates a real duplicate. Our original recovery plan (replay the same key after a failure) was therefore unsafe.

## Decision
Never rely on key replay for recovery. Recovery layers instead: (1) heal-from-log — XeroLogger::soleSuccessGuids with a single-GUID rule, verified against Xero's echo of embedded local identifiers; (2) on-Xero backstop — fetch by Type+Date paged and match client-side on the description JSON (Reference where-filters silently match nothing on BankTransactions — Xero defect); (3) write-ahead attempt rows + save-before-log ordering so an interrupted run is recoverable; (4) deterministic per-record keys (yh-op-/yh-dep-/... + localId) still used to de-duplicate WITHIN a token lifetime.

## Consequences
Duplicates cannot arise from retries; a committed-but-unconfirmed object is found by the next run instead of re-created. Cost: recovery requires reads of Xero (quota) and the backstop's client-side matching. The 22.5h Retry-After hang is separately fail-fasted + circuit-broken.