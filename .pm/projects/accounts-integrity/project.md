---
id: P-0020
slug: accounts-integrity
name: Accounts Integrity Programme — Xero posting & document generation
state: active
phase: build
created: 2026-07-10T21:16:32Z
updated: 2026-07-10T21:19:51Z
owner:
  kind: human
  name: Austin
goal: "Reduce accounts' reliance on IT: eliminate the generation/posting problems that force Austin to step in with manual data fixes, and make any remaining issue self-announcing and self-serviceable by accounts."
success_criteria:
  - "30 days post-release: zero entries in the invoice-integrity log channel from newly generated documents (or every entry triaged same-day)"
  - "30 days post-release: /doc-integrity mismatch list frozen at the historical set; validations page 100% PASS on new documents"
  - Posting runs complete with zero orphans/duplicates; daily digest received; run-issues page actioned within a day
  - All historical cleanup items dispositioned (voids done, 251316 fixed, C089676 reviewed)
  - No IT data-fix requests from accounts attributable to generation/posting defects
parent_project: null
related_projects: []
parent_ticket: T-0509
parent_pre_project: PP-011
kind: initiative
key_decisions: []
labels: []
order: 1024
problems:
  - Financial documents (invoices/credit notes) were generated with headers disagreeing with their line items — locally-rendered and Xero-rendered amounts diverged (the adjuster smear), credit notes doubled (itemRef collisions), VAT mis-stated (string codes, no-VAT contracts) — each one becoming a manual DB fix by IT after accounts flagged it
  - The Xero posting pipeline could duplicate transactions (double-click on post-all, no mutex), orphan documents (created in Xero but GUID never written back — 729 healed), and hang for 22h on rate limits; failures were silent until accounts noticed
  - IT time is consumed doing accounts data repairs instead of development; there is no systematic visibility of document/posting health — problems surface via accounts complaints
  - Internal test contracts leak into live accounts and Xero (real receivables for test data)
  - "Rollout of the fixes is high-risk: two stacked branches, data cleanup, config/RBAC steps, and behaviour changes to money documents"
goals:
  - "PRIMARY: reduce accounts' reliance on IT — no more Austin stepping in for manual DB fixes caused by generation/posting defects"
  - Every document header equals what its lines render on Xero, by construction; residuals visible on labeled lines
  - Every document posts to Xero exactly once; failures logged, digested, and self-healing on the next run
  - "Issues self-announce (daily digest, invoice-integrity log, /doc-integrity pages) and are triageable by accounts without IT: every flagged item links to the document, the Xero record, and the manual-refunds page"
  - IT retains fast investigation tooling (doc-integrity deep dive, replay harness, E2E harness) for the rare cases that do need engineering
cost_of_inaction: "Continued weekly IT time on manual accounts data fixes; recurring double/orphaned postings in Xero requiring hand-voiding (264 pairs one incident); customer-facing documents that disagree with Xero by up to thousands of pounds (C089676 pair: £3,412 headers vs ±£99 lines); VAT misstatement risk on adjustments; accounts trust erosion — every close takes longer; and the fixes already built (T-0534 merge-ready, T-0538 proven) rot on branches while live keeps generating the old failure classes."
scope_in:
  - "T-0534 posting pipeline: self-heal, heal-from-log, backstops, deterministic idempotency keys, mutexes, DB token store, daily cron + digest, run-issues/repair pages"
  - "T-0538 generation integrity: diff-engine fixes (composite keys, VAT, nominals), Xero-valid labeled adjustment lines, shadow invariant, writer guards (sign, VAT variant, contractType), investigation tooling (doc-integrity, replay, E2E), Xero deep-link helper"
  - "Release engineering: sequencing, params (xeroOrgShortCode), RBAC route grants, canary, hypercare monitoring"
  - "Historical data cleanup: test-doc voids, item 251316 (+£180), C089676 accounts review, live-since-snapshot check, 6 legacy positive credit items"
  - "Prevention follow-ons as project tickets: T-0542 (test contracts out of Xero), T-0541 (quote-builder pricing review), T-0539 (proformas), T-0540 (yahirenew/chl audit)"
scope_out:
  - Quote-builder session-writer behaviour changes (manual-price survival) — explicitly deferred by Austin; T-0541 review gates any change
  - Sister-project (yahirenew/chl) code changes — T-0540 audits first
  - Header-engine (recalculatePrice) semantics changes — deliberately untouched for risk
  - Xero chart-of-accounts or accounting-policy changes on live
  - Legacy (pre-go-live) document regeneration
workstream_ownership:
  - workstream: Code (branches, fixes, tooling)
    owner: Claude (agent) with Austin review
  - workstream: Release decisions & live operations
    owner: Austin
  - workstream: Data cleanup on live
    owner: Austin
  - workstream: Accounts liaison & verification
    owner: Austin (with accounts team)
  - workstream: Quote-builder review (T-0541)
    owner: Austin + Zsolt
version: 9
success_measures:
  - "Count of accounts-requested IT data fixes per month attributable to generation/posting — target 0 (baseline: recurring, e.g. 34-doc census + 264-pair void incident)"
  - Hours of Austin's time per month on accounts data repairs — target ~0
  - Documents on the /doc-integrity mismatch list — frozen at historical set, no new entries
  - Posting-run failures needing human action — digest/run-issues items actioned same-day, zero orphans/duplicates
---

# Accounts Integrity Programme — Xero posting & document generation

## Overview

Converted from pre-project PP-011.

## Why now

## Design

## Milestones
