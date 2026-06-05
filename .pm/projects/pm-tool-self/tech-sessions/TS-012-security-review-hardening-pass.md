---
id: TS-012
slug: security-review-hardening-pass
title: Security review + hardening pass
project: pm-tool-self
created: 2026-06-05T22:57:57Z
updated: 2026-06-05T22:58:41Z
decisions:
  - Enforced requireTeam authorization on EVERY mutating web server action (T-0253) — applied the existing projects/meetings pattern across decisions, defects, milestones, sprints, status, tech-sessions, timeplan, incidents, handover, conventions, tickets. Closed the "a logged-in non-member can edit any project" gap.
  - 'AUTH_SECRET: chose a random per-process fallback over fail-closed (T-0254) — removes the "forge a session with a published string" risk with ZERO outage risk (sessions reset on restart if no stable secret is set), rather than risking a login outage by hard-requiring a secret we could not verify was configured.'
  - Read paths now filter at the DATA layer via visibleProjectSlugs() (T-0256) — project pickers, ticket search, and the activity feed return only what the effective user may see, instead of loading everything and relying on route guards / client-side hiding.
  - "Input hardening (T-0255): git identity passed via GIT_AUTHOR_*/COMMITTER_* env not -c arg interpolation; a path-traversal guard (assertSafeSegment) on slug/id path components; inbound-email sender/subject escaped before markdown; Graph token-exchange errors no longer log the raw response body."
  - MCP write-tool authorization is deliberately DEFERRED — it needs a per-request identity in the MCP transport first, so it is a focused follow-up (T-0257 / T-0175), not a late-night rush. The web layer (the reachable surface) is hardened.
  - "No new ADR was filed for the security work: it enforces existing decisions (ADR-003 gates-must-bite, ADR-008/009 RBAC, ADR-025 MCP write-parity) rather than introducing new architecture; the two trade-offs above are recorded here."
actions:
  - text: "Finish T-0254: validate the session token signature/expiry in middleware + sign/expire the preview-as cookie"
    ticket: T-0254
  - text: Per-user MCP identity + write-tool authz + token rotation + audit logging
    ticket: T-0257
  - text: Dashboard preview-as data filtering (route-gated, lower-risk follow-up)
    ticket: T-0256
side_quests: []
problem: Before widening admin access (Ben) and connecting the tool to GitHub + real data, we ran a deliberate security review (T-0249) across auth, RBAC, the MCP, secrets, input handling, and data exposure, and fixed the actionable findings.
success_criterion: The critical + reachable high findings are fixed and live; the remaining (auth-middleware, per-user MCP identity) are scoped as follow-up tickets; the tool consistently enforces its own auth/access invariants.
phase: test
---

# TS-012: Security review + hardening pass

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
