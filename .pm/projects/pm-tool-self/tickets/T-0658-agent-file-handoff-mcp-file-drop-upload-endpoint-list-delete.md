---
id: T-0658
title: "Agent file handoff: MCP file-drop (upload endpoint + list/delete tools) for cross-session transfer"
type: feature
state: in_progress
created: 2026-07-23T23:10:31Z
updated: 2026-07-23T23:10:37Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[ ] POST /handoffs with a valid bearer uploads the request body to S3 under handoffs/<id>/ and returns {handoff_id, filename, size, expires_at, created_by}; the file round-trips byte-identical"
  - "[ ] POST /handoffs with no/invalid bearer returns 401; a body over the size cap is rejected with a clear error and no partial object left behind"
  - "[ ] pm_list_file_handoffs returns every current handoff with filename, size, note, created_by, created_at, expires_at, an `expired` flag, and a working short-lived presigned download_url (optionally filtered by handoff_id)"
  - "[ ] pm_delete_file_handoff removes the blob + meta.json for that id and is idempotent (deleting an unknown/already-deleted id doesn't error)"
  - "[ ] End-to-end: upload from one token, list+download+delete from another; after delete it no longer appears in the list"
  - "[ ] created_by attributes to the uploading token's principal (per-user token → dev; legacy shared token → 'shared')"
  - "[ ] Degrades gracefully when S3 isn't configured: upload + tools return a clear 'storage not configured' error rather than crashing"
out_of_scope:
  - Auto-delete-on-download / one-time semantics (receiver deletes explicitly; S3 lifecycle rule is the TTL backstop)
  - A web UI for handoffs
  - Presigned-PUT via AWS SDK (upload goes through the MCP server's own endpoint instead)
code_anchors:
  - path: comms/src/lib/s3.ts
    symbol: NEW listObjects(prefix) + getObjectText(key)
  - path: mcp-server/src/http.ts
    symbol: NEW POST /handoffs upload endpoint (reuses resolvePrincipal)
  - path: mcp-server/src/tools/handoff-tools.ts
    symbol: NEW pm_list_file_handoffs + pm_delete_file_handoff
  - path: mcp-server/src/server.ts
    symbol: register the two handoff tools
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260723-2310
    model: claude-opus-4-8
    started: 2026-07-23T23:10:37Z
    status: in_progress
labels:
  - mcp
  - infra
attention: null
version: 3
---

## Problem

There's no way for one agent session to hand a **file** to another via the tool.
The MCP surface only moves structured records + text (transcripts); attachments
are a web/S3-only path with no generic upload, no presigned download for
non-audio, and no delete. So "agent A builds an artifact → agent B (e.g. on the
GPU box) picks it up → cleans up" isn't possible. This blocked handing a built
bundle from the Mac session to a GPU-box session.

## Design

The MCP server (`mcp-server/src/http.ts`) is already an Express app with bearer
auth (per-user `pmMcpTokens` → attributable principal) and an `aws`-CLI S3
helper (`comms/src/lib/s3.ts`). Reuse both — no new dependency, no Next.js route.

1. **Upload — `POST /handoffs`** on the MCP Express server (bearer-authed via the
   existing `resolvePrincipal`). Streams the raw request body to a temp file,
   enforces a size cap, then `putObject`s the blob to `handoffs/<id>/<filename>`
   and a sidecar `handoffs/<id>/meta.json` ({id, filename, content_type, size,
   note, created_by=principal.dev, created_at, expires_at}). Returns JSON
   `{handoff_id, filename, size, expires_at, created_by}`. Filename/note/ttl come
   from `X-Filename` / `X-Note` / `X-TTL-Hours` headers. Not JSON-parsed (the
   global `express.json` only engages for application/json), so large binaries
   stream through.
2. **List/fetch — `pm_list_file_handoffs({handoff_id?})`** MCP tool: enumerates
   the `handoffs/` prefix, reads each `meta.json`, returns
   `[{handoff_id, filename, size, note, created_by, created_at, expires_at,
   expired, download_url}]` with a short-lived presigned GET url per handoff.
3. **Delete — `pm_delete_file_handoff({handoff_id})`** MCP tool: deletes the blob
   + meta; idempotent.

New `s3.ts` helpers: `listObjects(prefix)` and `getObjectText(key)` (both shell
to `aws`, matching the existing style).

### Flow
Sender: `curl -H "Authorization: Bearer $TOK" -H "X-Filename: bundle.zip" --data-binary @bundle.zip https://support.yahire.com/handoffs` → gets `handoff_id`.
Receiver: `pm_list_file_handoffs` → `download_url` → download → `pm_delete_file_handoff`.

## Out of scope (note as follow-ups)

- Auto-delete-on-download (one-time semantics) — receiver deletes explicitly; an
  S3 lifecycle rule on `handoffs/` is the TTL backstop.
- A web UI for handoffs.

## Notes

- New MCP tools require a client **session restart** to appear; the HTTP upload
  endpoint is live immediately on deploy.
- Same trust boundary as all MCP tools (any valid token = admin); `created_by`
  makes uploads attributable. Size-capped + dedicated prefix.
