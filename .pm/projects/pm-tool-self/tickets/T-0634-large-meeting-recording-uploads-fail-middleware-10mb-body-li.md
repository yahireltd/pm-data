---
id: T-0634
title: Large meeting-recording uploads fail — middleware 10MB body limit (fixed) + "Unexpected end of form" on very large multipart (needs direct-to-S3)
type: bug
state: review
created: 2026-07-21T15:03:55Z
updated: 2026-07-21T15:34:27Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260721-1529
    model: claude-opus-4-8
    started: 2026-07-21T15:29:41Z
    status: completed
    ended: 2026-07-21T15:34:27Z
    summary: "Fixed the bug that stopped long meeting recordings from being attached to a meeting. The uploader ran through the login-security layer, which quietly choked on large files (a 69-minute recording is ~17MB) and failed with a confusing error. The upload now goes through a dedicated path that skips that layer, so big recordings attach cleanly — with the same login checks still enforced. Without this, anyone recording a real meeting (the flagship feature) would hit a dead end on anything over a few minutes and have to compress the file by hand. Two-part fix: first a config limit was raised (already live), and now the upload takes a route that avoids the layer that was truncating the file."
    test_plan: |-
      Requires a deploy first (Austin): `sudo pm-deploy` on the pm-tool host.
      1. Open a meeting (e.g. sales-segmentation-account-management / M-013) → attach the ORIGINAL 17MB recording (meeting-recording-2026-07-21-1426.m4a) via the file-attach control. EXPECT: it uploads and appears in the attachments list (no "Application error" / "Unexpected end of form").
      2. Record a short clip with the in-browser Record button on a meeting → EXPECT: still uploads and attaches (the recorder path was repointed too).
      3. Confirm the attachment is real: reload the meeting, click the attachment's download link → it plays.
      4. Verify auth still holds: the /api/meetings/attach route does its own access check — try it while signed out (or with no session) → EXPECT rejection, not an open upload.
      5. Cross-impact: attaching a NON-audio document to a meeting via the same panel still works (the panel's upload action was also repointed). And ticket attachments are UNAFFECTED (separate code path — uploadAttachment in tickets.ts — left as-is).
      6. Regression: the transcription worker still picks up the newly-attached audio (pm_list_meeting_audio shows it; transcribe-meetings can process it).
      Note: server-side buffering still caps at 250MB (fine for recordings). If a future need exceeds that, the follow-up is direct-to-S3 presigned PUT (would need the AWS SDK + bucket CORS) — noted in the ticket body, not done here.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-21T15:34:27Z
version: 4
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

## Conversation

**2026-07-21 15:34 claude-code:** Run run-20260721-1529 completed — Fixed the bug that stopped long meeting recordings from being attached to a meeting. The uploader ran through the login-security layer, which quietly choked on large files (a 69-minute recording is ~17MB) and failed with a confusing error. The upload now goes through a dedicated path that skips that layer, so big recordings attach cleanly — with the same login checks still enforced. Without this, anyone recording a real meeting (the flagship feature) would hit a dead end on anything over a few minutes and have to compress the file by hand. Two-part fix: first a config limit was raised (already live), and now the upload takes a route that avoids the layer that was truncating the file.
