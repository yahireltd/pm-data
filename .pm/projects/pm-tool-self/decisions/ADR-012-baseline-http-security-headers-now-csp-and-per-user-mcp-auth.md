---
id: ADR-012
slug: baseline-http-security-headers-now-csp-and-per-user-mcp-auth
title: Baseline HTTP security headers now; CSP and per-user MCP auth deferred as known, accepted gaps
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0175
  - T-0162
---

## Context
Going public on support.yahire.com surfaced two hardening questions with no clean immediate answer. First, browser-level headers: the app leaked `x-powered-by: Next.js` and set no HSTS/anti-clickjacking/anti-sniffing headers. A full Content-Security-Policy was tempting but a strict CSP needs per-request nonces for Next.js's inline scripts — a careful, separate change that, done hastily, breaks the app. Second, the remote MCP's single shared bearer token grants full, unscoped access while the web app has real three-tier RBAC — a deliberate asymmetry that needed to be named rather than silently shipped.

## Decision
Apply a baseline set of global security headers in `next.config.ts` to every route — HSTS (`max-age=31536000; includeSubDomains; preload`), `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`, and a restrictive `Permissions-Policy` — and disable `poweredByHeader` to stop framework fingerprinting. Explicitly DEFER CSP, documenting why (nonce requirement) and tracking it as a follow-up rather than shipping a broken or permissive policy. Separately, formally accept the shared-MCP-token risk: the MCP token is single, shared, and full-access with no per-user RBAC, so it is handed only to trusted devs/PMs (stakeholders use the web UI), and per-user MCP auth is named as the future direction rather than a current guarantee.

## Consequences
Every response is hardened against the common transport/clickjacking/sniffing/referrer/fingerprinting classes immediately, with no app-breakage risk. We accept that we ship WITHOUT a CSP — the largest remaining browser-side XSS mitigation is absent until the nonce work lands — and that the MCP remains a coarser, less-safe access path than the web app for as long as the shared token stands. Naming these gaps is the trade: the posture is honest and the follow-ups (CSP with nonces, per-user MCP auth) are on record rather than forgotten.