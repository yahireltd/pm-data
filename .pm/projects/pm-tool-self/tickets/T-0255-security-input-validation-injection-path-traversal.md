---
id: T-0255
title: "[security] Input validation: injection & path traversal"
type: chore
state: done
created: 2026-06-05T21:42:38Z
updated: 2026-06-05T22:25:51Z
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
  - "[x] backup.ts: sanitise/validate the admin name+email before they are interpolated into git -c args (or use GIT_AUTHOR_NAME/EMAIL env) — no git-arg injection"
  - "[x] All slug/id params validated with a strict regex (^[a-z0-9_-]+$ etc.) in MCP tool inputs + path builders (cli/mcp paths.ts) to block ../ path traversal"
  - "[x] Inbound email header values (fromName, subject) are escaped before being embedded in ticket markdown (comms/src/inbound/parser.ts)"
  - "[x] Graph token-exchange error messages no longer include the raw response body in persisted logs (comms/src/lib/graph.ts)"
out_of_scope: []
code_anchors:
  - path: cli/src/lib/paths.ts
    symbol: assertSafeSegment
  - path: mcp-server/src/lib/paths.ts
    symbol: assertSafeSegment
  - path: web/app/_actions/backup.ts
  - path: comms/src/inbound/parser.ts
    symbol: escapeHeaderField
  - path: comms/src/lib/graph.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2223
    model: claude-opus-4-8
    started: 2026-06-05T22:23:32Z
    status: completed
    ended: 2026-06-05T22:23:48Z
    summary: "Tightened up how the tool handles untrusted input, from the security review. Four hardening changes: (1) the \"back up data\" button no longer feeds the admin's name/email into git command flags — it passes them safely as environment values and strips any odd characters, so a weird name can't tamper with the backup command. (2) Added a guard that rejects sneaky file paths (containing \"/\", \"..\", etc.) when the tool turns an id or project name into a file location — this blocks \"path traversal\" attempts to read/write outside the intended folders. (3) Incoming support emails now have their sender name and subject cleaned before they're written into a ticket, so a malicious email can't inject formatting/HTML. (4) When the email/calendar login fails, we no longer copy the raw server response into our logs, so sensitive details don't pile up in log files. Normal use is unchanged."
    test_plan: 'Back up via the UI as an admin — it still commits/pushes, with the admin shown as author (control characters stripped). A crafted id/slug containing "/" or ".." passed to a path builder throws "invalid path segment" (verified the guard accepts all real ids: kebab slugs + T-/ADR-/M-/TS-/PP-/SN-/MS-, and rejects traversal). An inbound email with HTML/markdown in the sender name or subject lands in the ticket escaped, not rendered. A forced email-login failure logs only the status code, not the response body. Build green.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

## Problem

Security review (T-0249): CRITICAL git-arg injection via the authenticated user’s name/email in backup.ts; HIGH path-traversal via unvalidated project slug in path builders (e.g. pm_create_ticket project="../../.."); MEDIUM unescaped inbound email headers into markdown + Graph error bodies written to logs.

## Context

Files: web/app/_actions/backup.ts (git -c), mcp-server/src/lib/paths.ts + tool input schemas, comms/src/inbound/parser.ts, comms/src/lib/graph.ts.

## Conversation

**2026-06-05 22:23 claude:** Run run-20260605-2223 completed — Tightened up how the tool handles untrusted input, from the security review. Four hardening changes: (1) the "back up data" button no longer feeds the admin's name/email into git command flags — it passes them safely as environment values and strips any odd characters, so a weird name can't tamper with the backup command. (2) Added a guard that rejects sneaky file paths (containing "/", "..", etc.) when the tool turns an id or project name into a file location — this blocks "path traversal" attempts to read/write outside the intended folders. (3) Incoming support emails now have their sender name and subject cleaned before they're written into a ticket, so a malicious email can't inject formatting/HTML. (4) When the email/calendar login fails, we no longer copy the raw server response into our logs, so sensitive details don't pile up in log files. Normal use is unchanged.

---

**2026-06-05 22:25 — you**

done
