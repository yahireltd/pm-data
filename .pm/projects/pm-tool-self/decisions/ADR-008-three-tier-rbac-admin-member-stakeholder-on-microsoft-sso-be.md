---
id: ADR-008
slug: three-tier-rbac-admin-member-stakeholder-on-microsoft-sso-be
title: Three-tier RBAC (admin/member/stakeholder) on Microsoft SSO, behind a login wall
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0172
---

## Context
pm-tool began as a single-operator, no-login tool: anyone who could reach it could read and mutate everything. To put it in front of real PMs, devs, and external stakeholders we needed identity plus authorization. Options considered: (a) a homegrown username/password store — rejected, it owns credentials, password resets, and a new attack surface for no benefit; (b) a flat allow-list of "who can log in" with no per-resource scoping — too coarse, stakeholders would see every project and every ticket; (c) full per-resource ACLs on every entity — more than the data model needs and heavy to maintain by hand. We already use Microsoft Graph (Entra app registration) for email and calendar, so an Entra identity was effectively free.

## Decision
Adopt Microsoft SSO via Auth.js v5 (MicrosoftEntraID provider, creds from env or AWS Secrets Manager `pmToolAuth`, identity keyed on email with preferred_username/upn fallback) and a three-tier access model resolved purely by the signed-in email in `web/app/_lib/access.ts`: admin (config `access.admins`, full access everywhere + global pages), member (a project's `team:` list, full access to that project only), stakeholder (a project's email-channel `stakeholders:`, read + comment on that project only). A `middleware.ts` login wall redirects unauthenticated requests to `/me` (cheap cookie-presence check at the edge; real validation + role gating happens in the Node runtime). Authorization is enforced per action, not just per page: every PM-owned mutation calls `requireTeam(slug)`, which is project-scoped so it also closes the cross-project gap. SSO is non-breaking — with no Entra creds the app initializes with zero providers and keeps running. Identity is resolved through a single seam, `getCurrentIdentity()`, so login mechanism and authz policy stay decoupled.

## Consequences
Access is now a real, enforced model with three distinct UX surfaces, and the policy functions are pure/testable. We accept a hard dependency on Entra/Azure availability for login (a dev-only `pm_identity` cookie fallback exists but is not for production), and on email being a stable, unique identity key. Membership is hand-edited in YAML (`team:`/`stakeholders:`/`access.admins`) — fine at current scale but not self-service. The edge middleware only checks for a session cookie's presence, so the Node-runtime layout must do the authoritative check — a split that must be kept honest. CSP is not part of this model. This is the web app's RBAC only; the remote MCP is a separate, coarser surface (see the MCP and shared-token ADRs).