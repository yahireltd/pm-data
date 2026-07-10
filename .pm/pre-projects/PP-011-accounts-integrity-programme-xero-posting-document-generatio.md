---
id: PP-011
slug: accounts-integrity-programme-xero-posting-document-generatio
title: Accounts Integrity Programme — Xero posting & document generation
state: converted
created: 2026-07-10T21:14:58Z
updated: 2026-07-10T21:16:32Z
stakeholders: []
source_tickets:
  - T-0509
  - T-0534
  - T-0538
summary: "Programme wrapping the chain of work that began with the accounts double-click on /xero/post duplicating transactions (T-0509), led through the posting-pipeline hardening (T-0534: orphaned invoices, double-post attempts, self-heal layers, cron + digest), and into document-generation integrity (T-0538: broken invoices/credit notes, the adjuster smear, charge-canceller double-counts, VAT handling, investigation tooling). Goal: eliminate the class of accounts issues that IT resolves with manual data fixes — every financial document generated correctly, posted to Xero exactly once, and any divergence self-announcing instead of being discovered by accounts. High rollout risk: release sequencing (534 before/with 538), data cleanup, RBAC/params steps, and a monitored hypercare period."
owner:
  kind: human
  name: Austin
version: 4
problems:
  - Financial documents (invoices/credit notes) were generated with headers disagreeing with their line items — locally-rendered and Xero-rendered amounts diverged (the adjuster smear), credit notes doubled (itemRef collisions), VAT mis-stated (string codes, no-VAT contracts) — each one becoming a manual DB fix by IT after accounts flagged it
  - The Xero posting pipeline could duplicate transactions (double-click on post-all, no mutex), orphan documents (created in Xero but GUID never written back — 729 healed), and hang for 22h on rate limits; failures were silent until accounts noticed
  - IT time is consumed doing accounts data repairs instead of development; there is no systematic visibility of document/posting health — problems surface via accounts complaints
  - Internal test contracts leak into live accounts and Xero (real receivables for test data)
  - "Rollout of the fixes is high-risk: two stacked branches, data cleanup, config/RBAC steps, and behaviour changes to money documents"
goals:
  - Zero manual DB fixes for accounts arising from document generation or Xero posting
  - Every document header equals what its lines render on Xero, by construction, with residuals visible on labeled lines
  - Every document posts to Xero exactly once; failures are logged, digested, and self-heal on the next run
  - Divergences self-announce (invoice-integrity log channel, /doc-integrity pages, daily digest) instead of being discovered by accounts
  - IT holds tooling to investigate any historical document in minutes (doc-integrity, replay harness, E2E harness)
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
success_criteria:
  - "30 days post-release: zero entries in the invoice-integrity log channel from newly generated documents (or every entry triaged same-day)"
  - "30 days post-release: /doc-integrity mismatch list frozen at the historical set; validations page 100% PASS on new documents"
  - Posting runs complete with zero orphans/duplicates; daily digest received; run-issues page actioned within a day
  - All historical cleanup items dispositioned (voids done, 251316 fixed, C089676 reviewed)
  - No IT data-fix requests from accounts attributable to generation/posting defects
cost_of_inaction: "Continued weekly IT time on manual accounts data fixes; recurring double/orphaned postings in Xero requiring hand-voiding (264 pairs one incident); customer-facing documents that disagree with Xero by up to thousands of pounds (C089676 pair: £3,412 headers vs ±£99 lines); VAT misstatement risk on adjustments; accounts trust erosion — every close takes longer; and the fixes already built (T-0534 merge-ready, T-0538 proven) rot on branches while live keeps generating the old failure classes."
milestones:
  - title: Incident containment & manual repairs (historical)
    acceptance_criteria:
      - T-0509 duplicate overpayments voided (264 pairs, done 2026-05-20)
      - 729 orphaned invoices healed via /xero/heal-invoice-numbers
      - Root causes documented on tickets
  - title: Posting pipeline hardened on live (T-0534)
    acceptance_criteria:
      - Branch merged to master and deployed; migration run; /xero/login on live
      - Run-post mutex, self-heal, backstops, DB token store active in production
      - Daily cron 06:00 running with digest received; canary posting run clean
      - Linked-email data fixes applied
      - Sandbox refresh cron re-enabled
    target_date: 2026-07-24
  - title: Document generation integrity on live (T-0538)
    acceptance_criteria:
      - Branch merged (after/with T-0534) and deployed
      - RBAC route permission granted for /doc-integrity; xeroOrgShortCode param set on live
      - Validations page shows 100% PASS for documents generated in the first 2 weeks
      - invoice-integrity log channel quiet or triaged same-day
      - Austin has completed the deep-dive sampling checklist (4 parts)
    target_date: 2026-08-07
  - title: Historical data cleanup complete
    acceptance_criteria:
      - Test docs voided locally (69663/75664/70029/70030) and INV 69663 voided in Xero
      - Item 251316 corrected (+£180 → −£180) and CR 78307 dispositioned
      - C089676 pair reviewed by accounts and dispositioned
      - "Live-since-snapshot mismatch SQL run: clean or all rows dispositioned"
      - Decision recorded on the 5 legacy 2024 positive credit items
    target_date: 2026-08-07
  - title: Prevention follow-ons closed
    acceptance_criteria:
      - "T-0542: test contracts provably excluded from posting + existing leakage cleaned"
      - "T-0541: quote-builder pricing review decided with Zsolt (manual-price survival + package pricing), with regression tests preceding any change"
      - T-0539 proformas and T-0540 sister-project audit dispositioned (done or consciously parked with rationale)
    target_date: 2026-09-04
  - title: Steady state declared (hypercare exit)
    acceptance_criteria:
      - "30 consecutive days: no accounts data-fix requests attributable to generation/posting"
      - Monitoring routine documented and owned (digest, run-issues, doc-integrity/validations)
      - Project retro held; remaining nice-to-haves ticketed to backlog
    target_date: 2026-09-11
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
kickoff_held: true
scoping_held: true
project: accounts-integrity
---

# PP-011: Accounts Integrity Programme — Xero posting & document generation

## Summary

Programme wrapping the chain of work that began with the accounts double-click on /xero/post duplicating transactions (T-0509), led through the posting-pipeline hardening (T-0534: orphaned invoices, double-post attempts, self-heal layers, cron + digest), and into document-generation integrity (T-0538: broken invoices/credit notes, the adjuster smear, charge-canceller double-counts, VAT handling, investigation tooling). Goal: eliminate the class of accounts issues that IT resolves with manual data fixes — every financial document generated correctly, posted to Xero exactly once, and any divergence self-announcing instead of being discovered by accounts. High rollout risk: release sequencing (534 before/with 538), data cleanup, RBAC/params steps, and a monitored hypercare period.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
