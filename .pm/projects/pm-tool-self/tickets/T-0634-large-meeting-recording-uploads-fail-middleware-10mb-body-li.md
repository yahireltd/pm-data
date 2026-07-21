---
id: T-0634
title: Large meeting-recording uploads fail — middleware 10MB body limit (fixed) + "Unexpected end of form" on very large multipart (needs direct-to-S3)
type: bug
state: triaged
created: 2026-07-21T15:03:55Z
updated: 2026-07-21T15:03:55Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - middleware body limit raised so uploads aren't capped at 10MB (done, deployed)
  - Meeting-recording upload uses a direct-to-S3 presigned PUT, bypassing the auth middleware + app buffering
  - S3 bucket CORS allows PUT from the app origin
  - A 60+ minute (>17MB) recording uploads successfully end to end
  - Attachment metadata still recorded on the meeting; the transcription worker still picks it up
out_of_scope: []
code_anchors:
  - path: web/next.config.ts
    note: "middlewareClientMaxBodySize: 256mb (root cause 1 fix, shipped)"
  - path: web/middleware.ts
    note: auth login-wall buffers body; matcher covers page POSTs incl. uploads — the reconstruction that breaks on large multipart
  - path: web/app/_actions/meetings.ts
    symbol: uploadMeetingAttachment
    note: current server-action buffering upload; replace with presigned-PUT flow
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem
Uploading a real meeting recording to a meeting failed. A 69-min recording is ~17MB; the browser showed a generic error ("no reason stated").

## Root cause 1 — FIXED + DEPLOYED (commit 20a0b6e)
Server log: `Request body exceeded 10MB ... middlewareClientMaxBodySize`. `web/middleware.ts` (the auth login-wall) buffers the request body before the upload server action, and its limit defaults to **10MB** — separate from `serverActions.bodySizeLimit` (already 256mb). Fix: set `experimental.middlewareClientMaxBodySize: "256mb"` in `web/next.config.ts`. Deployed by Austin; the 10MB error is gone.

## Root cause 2 — STILL OPEN
After the fix, the 17MB upload now throws `Error: Unexpected end of form` (digest 505434680). The auth middleware runs on every page POST (matcher includes the meeting route) and Next reconstructs the buffered multipart body via `NextResponse.next({ request: { headers } })`; for very large multipart bodies that reconstruction breaks.

## Proper fix
Move meeting-recording upload to a **direct-to-S3 presigned PUT**: mint a presigned URL (tiny request), browser PUTs the file straight to S3 (bypasses the app process AND the auth middleware entirely), then a small server call records the attachment metadata on the meeting. Needs S3 bucket CORS for PUT from the app origin. This also removes the "buffer through the web process's memory" ceiling noted in `uploadMeetingAttachment`.

## Workaround for now
Compress long recordings under ~8MB before upload, or transcribe locally + attach the transcript (the actual value).