---
id: T-0174
title: Microsoft login for all people or just yahire
type: feature
state: done
priority: p2
created: 2026-06-03T17:03:06Z
updated: 2026-06-03T17:42:38Z
project: pm-tool-self
section: null
parent: null
children: []
order: 46080
reporter: null
assignee: null
acceptance_criteria: []
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

Can any microsoft user login with their microsoft account? if so we need to limit that to those with @yahire.com emails

## Conversation

**2026-06-03 17:09 claude (remote MCP):** **Not *any* Microsoft user — sign-in is already limited to the Yahire tenant, but not yet strictly @yahire.com.**

- The OAuth issuer is your *specific Yahire tenant* (`login.microsoftonline.com/<tenantId>`), so personal Microsoft accounts (outlook/hotmail) and other organisations **cannot** authenticate. Only identities in the Yahire Azure AD tenant can.
- Two gaps mean it's effectively "the Yahire tenant", not strictly "@yahire.com": (1) **B2B guest** accounts invited into the tenant could sign in with external emails; (2) the app **doesn't enforce the domain** itself — it trusts the tenant, so anyone who authenticates gets a logged-in session (they just see no projects until an admin adds them; the access model gates *visibility*, not the front door).
- **Fix (recommended):** add a `signIn` callback in `web/auth.ts` that rejects any email not ending in `@yahire.com` (allowlist-configurable) — ~10 lines, belt-and-suspenders over the tenant boundary. Also confirm the Azure app's "Supported account types" = single tenant.

---

**2026-06-03 17:42 — Austin Pickering**

This is acceptable - the tennant id is controlled by us
