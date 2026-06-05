---
id: T-0254
title: "[security] Auth & session hardening (AUTH_SECRET, middleware, preview-as cookie)"
type: chore
state: triaged
created: 2026-06-05T21:42:22Z
updated: 2026-06-05T21:42:22Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - 'AUTH_SECRET fails closed: no hardcoded "pm-tool-dev-insecure-secret-change-me" fallback — error at startup if unset (web/auth.ts)'
  - Middleware validates the session JWT signature + expiry, not just cookie presence (web/middleware.ts); the pm_identity fallback is dev-only + clearly gated
  - preview-as cookie is signed/encrypted + time-bounded (web/app/_lib/access.ts); document the can-only-restrict invariant
  - CSRF strategy documented (Next.js server-action protection) with a note for any custom endpoints
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

Security review (T-0249) HIGH: AUTH_SECRET falls back to a known string (forgeable sessions if misconfigured); the middleware only checks for a cookie’s presence, not its cryptographic validity; the preview-as cookie is an unsigned plain cookie.

## Context

Files: web/auth.ts (secret fallback), web/middleware.ts (cookie presence check), web/app/_lib/access.ts (PREVIEW_COOKIE), web/app/_lib/identity.ts (silent catch fallback).