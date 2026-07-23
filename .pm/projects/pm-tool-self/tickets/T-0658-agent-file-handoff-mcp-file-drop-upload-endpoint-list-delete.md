---
id: T-0658
title: "Agent file handoff: MCP file-drop (upload endpoint + list/delete tools) for cross-session transfer"
type: feature
state: review
created: 2026-07-23T23:10:31Z
updated: 2026-07-23T23:15:38Z
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
    status: completed
    progress:
      - at: 2026-07-23T23:14:57Z
        note: "Built: s3.ts listObjects/getObjectText helpers; POST /handoffs upload endpoint on the MCP Express server (bearer-authed, streams to S3, 500MB cap); pm_list_file_handoffs + pm_delete_file_handoff tools; registered both; added tests. mcp-server + comms typecheck clean; handoff + tools-list tests pass. The round-trip/cross-project failures are pre-existing (need a local .pm/ that doesn't exist on this Mac). Committing + pushing; Austin deploys."
    ended: 2026-07-23T23:15:38Z
    summary: "The tool can now hand a whole FILE from one agent session to another — until now it could only move records and text, so a file built in one session (e.g. on the office Mac) had no way to reach an agent working elsewhere (e.g. the home GPU box). A session uploads a file to a new, password-protected drop on the tool's server; a second session sees what's waiting, downloads it with a short-lived private link, and deletes it once collected. Everything is stored temporarily on our existing secure file storage, capped in size, and stamped with who uploaded it. If we'd done nothing, moving a build artifact between sessions would still mean emailing it to a human to carry across by hand. Benefit: true agent-to-agent handoff of files, inside the tool."
    test_plan: |-
      Deploy first (Austin). NOTE: the two new tools (pm_list_file_handoffs, pm_delete_file_handoff) only appear after a Claude Code SESSION RESTART; the /handoffs upload endpoint is live immediately on deploy.

      UPLOAD (from any machine with a valid MCP bearer token):
      1. `echo "hello handoff" > /tmp/t.txt` then:
         `curl -sS -H "Authorization: Bearer $TOK" -H "X-Filename: t.txt" -H "X-Note: smoke test" -H "Content-Type: text/plain" --data-binary @/tmp/t.txt https://support.yahire.com/handoffs`
         → expect JSON `{ok:true, handoff_id, filename:"t.txt", size, expires_at, created_by}`.
      2. No/invalid token → HTTP 401. Empty body → 400. (Size cap is 500MB; a >500MB body → 413 with no partial object left behind.)

      FETCH (restart the session so the new tools load, then):
      3. `pm_list_file_handoffs` → lists the handoff with filename, size, note, created_by, created_at, expires_at, `expired:false`, and a `download_url`.
      4. `curl -sS "<download_url>" -o /tmp/got.txt` → byte-identical to the original (`diff /tmp/t.txt /tmp/got.txt` is clean). Optionally filter with `pm_list_file_handoffs {handoff_id}`.

      DELETE:
      5. `pm_delete_file_handoff {handoff_id}` → `{deleted: 2}` (blob + meta). Call it again → `{deleted: 0}` (idempotent, no error).
      6. `pm_list_file_handoffs` → the handoff is gone.

      REAL PAYLOAD (the reason this exists):
      7. Upload the GPU bundle: `curl ... -H "X-Filename: pm-voice-gpu-code.zip" -H "Content-Type: application/zip" --data-binary @/tmp/pm-voice-gpu-code.zip https://support.yahire.com/handoffs`, then from the GPU-box session list → download → unzip → delete. (81MB; confirms large binaries stream through, unaffected by the 4MB JSON body limit because the content-type isn't application/json.)

      CROSS-IMPACT:
      8. attribution: created_by = the uploading token's principal (per-user token → the dev's email; legacy shared token → "shared").
      9. Regression: existing /mcp JSON-RPC and /healthz unchanged; existing tools unaffected (only 2 tools + 1 endpoint added). If PM_S3_BUCKET were unset, upload + tools return a clear "storage not configured" error rather than crashing (covered by tests/handoff-tools.test.ts).

      Automated: `bun test tests/tools-list.test.ts tests/handoff-tools.test.ts` passes (tools registered + graceful degradation). Other suite files need a local .pm/ fixture that doesn't exist off-server — pre-existing, unrelated.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - mcp
  - infra
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-23T23:15:38Z
version: 5
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

## Conversation

**2026-07-23 23:15 claude-code:** Run run-20260723-2310 completed — The tool can now hand a whole FILE from one agent session to another — until now it could only move records and text, so a file built in one session (e.g. on the office Mac) had no way to reach an agent working elsewhere (e.g. the home GPU box). A session uploads a file to a new, password-protected drop on the tool's server; a second session sees what's waiting, downloads it with a short-lived private link, and deletes it once collected. Everything is stored temporarily on our existing secure file storage, capped in size, and stamped with who uploaded it. If we'd done nothing, moving a build artifact between sessions would still mean emailing it to a human to carry across by hand. Benefit: true agent-to-agent handoff of files, inside the tool.
