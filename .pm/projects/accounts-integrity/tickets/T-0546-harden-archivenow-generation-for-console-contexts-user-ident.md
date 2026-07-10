---
id: T-0546
title: Harden archiveNow + generation for console contexts (user-identity crash)
type: chore
state: triaged
created: 2026-07-10T21:47:55Z
updated: 2026-07-10T21:47:55Z
project: accounts-integrity
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: agent
  name: claude-code (T-0538 session)
  channel: code review
assignee: null
acceptance_criteria:
  - archiveNow and generateInvoiceWithItems run in a bare console app without the user-component workaround (attributed to user 0 / SYSTEM)
  - Web behaviour unchanged (identity still recorded when present)
  - E2E harness workaround removed and harness still green
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - hardening
  - t-0538-follow-on
attention: null
version: 1
---

## Problem

`YaContracts::archiveNow` dereferences `Yii::$app->user->identity->id` unguarded, and `generateInvoiceWithItems` reads `Yii::$app->user->isGuest`. In a console context (cron, future automation) the `user` component doesn't exist → hard crash. Today no console path generates invoices, so it's latent — but the T-0538 E2E harness had to work around it by attaching a web `User` component and calling `setIdentity()` manually, which proves the trap is real.

## Fix direction
Guard both: `Yii::$app->has('user') && !Yii::$app->user->isGuest ? identity->id : 0` (0 = the existing 'system/website' convention in acceptedchanges/archives). Keeps behaviour identical for web flows; makes console generation possible without the harness workaround.