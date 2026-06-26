---
id: T-0480
title: "Prereq: indexed ya_customers.email_domain column + centralised personal-domain list (home-row rollup plumbing)"
type: chore
state: triaged
created: 2026-06-26T14:59:04Z
updated: 2026-06-26T15:00:38Z
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
  - ya_customers has an indexed email_domain VARCHAR(190) column, backfilled from existing email via LOWER(SUBSTRING_INDEX(email,'@',-1)), and kept in sync on insert/update (model beforeSave or DB trigger).
  - The account-level domain rollup (ya_contracts + quotes -> economic account) keys back to ya_customers.email_domain via an INDEXED path, not a per-row SUBSTRING_INDEX full-table scan.
  - PERSONAL_EMAIL_DOMAINS is centralised to params['personalEmailDomains'] (today a hardcoded ~35-entry array inside YaCustomers.php incl. yahire.com) so live code, the engine and the simulator share one list and cannot diverge.
  - CustomerSalesScores.php docblock updated to include events_per_year (migration defines it) and the future classification column.
out_of_scope: []
code_anchors: []
relates: []
blocks:
  - T-0457
  - T-0479
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
---

## Why

Hard predecessor for the account-level model ([[T-0457]]) and its what-if simulator ([[T-0479]]). The home-row rollup (the economic account = email domain) needs a real indexed `email_domain` — verified to **not exist** anywhere in `ya_customers` / `ya_contracts` / `quotes` / migrations today. The scores page derives it on the fly (`SalesScoresController.php:121`, `LOWER(SUBSTRING_INDEX(cu.email,'@',-1))`), which is an unindexed scan. Until this lands, the recompute + fact-frame rollup is a full scan and the simulator's "runnable / sub-100ms" claim does **not** hold.

## Scope
- Migration: add `ya_customers.email_domain VARCHAR(190)` + index; backfill; keep-in-sync (beforeSave/trigger).
- Index the contracts/quotes → domain derivation path (their tables store raw `email`, which can differ from the customer's — key back to `ya_customers.email_domain`).
- Centralise the personal-domain list to `params['personalEmailDomains']`.
- Fix the `CustomerSalesScores` docblock.

See `~/Documents/P-0018-account-level-blend-addendum.md` §F.