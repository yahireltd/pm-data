---
id: T-0249
title: Security review / hardening pass
type: spike
state: done
created: 2026-06-05T21:24:45Z
updated: 2026-06-05T22:00:50Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Documented review of: Microsoft SSO auth + session handling, the admin/member/stakeholder RBAC (incl. preview-as), and where requireTeam/isAdmin gate mutations"
  - "[x] Review the remote MCP bearer-token auth (ADR-010/012) — token scope, rotation, per-user identity gap"
  - "[x] Review secrets handling (AWS Secrets Manager pmToolAuth incl. the new tokenGithubFine; the read-only GitHub token scope)"
  - "[x] Review input validation / authz on web server actions + MCP write tools (can a non-member mutate? can preview-as escalate?)"
  - "[x] Note the agent-policy guardrails (T-0247) + recommend GitHub branch protection on production branches"
  - "[x] Output: a prioritised findings list (severity) with concrete fixes, filed as follow-up tickets"
out_of_scope: []
code_anchors:
  - path: web/auth.ts
  - path: web/middleware.ts
  - path: web/app/_lib/access.ts
  - path: web/app/_actions/backup.ts
  - path: mcp-server/src/http.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2144
    model: claude-opus-4-8
    started: 2026-06-05T21:44:11Z
    status: completed
    ended: 2026-06-05T21:44:33Z
    summary: "We ran a security review across six areas: signing in, who-is-allowed-to-do-what, the AI/agent connection, how secrets are stored, how incoming data is handled, and what each role can see. It found real, worth-fixing issues (3 serious, 11 high, 9 medium) — none being actively exploited, because the tool sits behind a company login with a small, trusted team, but they matter as more people get access. The most important: several \"edit\" actions don't first check you're a member of the project, so a logged-in person could change projects that aren't theirs; the agent connection has no per-person identity or audit trail; a couple of spots could be abused with crafted input; sign-in has an insecure fallback password if mis-set-up; and a few screens load everything before deciding what you're allowed to see. We turned the findings into five prioritised tickets to work through. Good news: the core edit actions on projects/meetings, and how the web app renders text safely, are already done right — this is hardening, not a rebuild."
    test_plan: "The findings + concrete fixes are filed as five tickets: T-0253 (authorization on every edit action — web + agent connection), T-0254 (sign-in/session hardening), T-0255 (input validation: injection + path traversal), T-0256 (server-side access filtering on read screens), T-0257 (agent-connection hardening: identity, rotation, audit). Each carries acceptance criteria + the specific files. Verification of each fix happens in its own ticket; this spike's \"done\" = the prioritised, actionable list exists."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Findings + fixes filed as prioritised tickets T-0253–T-0257; full review detail in this run summary + each ticket body.
labels: []
attention: null
---

## Problem

As we add admins (Ben) and connect the tool to GitHub + real data, we should do a deliberate security review rather than assume it's fine. Is it possible — yes; this spike scopes + runs it.

## Context

Surfaces to examine: web (Next.js server actions, RBAC in web/app/_lib/access.ts, auth.ts Microsoft Entra), the remote MCP (bearer token, loopback+Caddy, no per-user identity — ADR-010/012), secrets (AWS Secrets Manager), the new read-only GitHub token (T-0243), and the agent-policy gate (T-0247). Deliverable is findings + prioritised fix tickets, not a rewrite.

## Conversation

**2026-06-05 21:44 claude:** Run run-20260605-2144 completed — We ran a security review across six areas: signing in, who-is-allowed-to-do-what, the AI/agent connection, how secrets are stored, how incoming data is handled, and what each role can see. It found real, worth-fixing issues (3 serious, 11 high, 9 medium) — none being actively exploited, because the tool sits behind a company login with a small, trusted team, but they matter as more people get access. The most important: several "edit" actions don't first check you're a member of the project, so a logged-in person could change projects that aren't theirs; the agent connection has no per-person identity or audit trail; a couple of spots could be abused with crafted input; sign-in has an insecure fallback password if mis-set-up; and a few screens load everything before deciding what you're allowed to see. We turned the findings into five prioritised tickets to work through. Good news: the core edit actions on projects/meetings, and how the web app renders text safely, are already done right — this is hardening, not a rebuild.

**2026-06-05 21:54 claude:** **What to do with this ticket — nothing to build here.** This *was* the security review, and it's finished. A "spike" like this is a research task: its job was to find the issues and turn them into fixable tickets, which it did. So:

1. **This ticket (T-0249):** just **confirm and close** it — the review itself is done.
2. **The actual work** is the 5 fix tickets it produced, in rough priority order. They're sitting in *triaged* for you to schedule (or hand to an agent):
   - **T-0253** — Make every "edit" action check you're a member of the project (the most important one)
   - **T-0255** — Input validation: injection + path-traversal hardening
   - **T-0254** — Sign-in / session hardening (fallback secret, cookie checks)
   - **T-0256** — Make list/search screens hide data you shouldn't see
   - **T-0257** — Agent-connection (MCP) hardening: per-person identity, audit log, token rotation

**Urgency:** none is on fire — the tool sits behind the company login with a small, trusted team — but they're worth working through as more people get access. Happy to fix any/all of them when you want; I'd do them one at a time with a review each, since it's security-sensitive code.

---

**2026-06-05 22:00 — you**

created new sprint for security work
