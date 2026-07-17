---
id: T-0597
title: Long meeting recordings fail to upload and the audio is lost (2MB action limit + no retry/download)
type: bug
state: done
created: 2026-07-16T12:58:08Z
updated: 2026-07-17T12:33:36Z
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
  - "[x] A 10+MB recording uploads successfully (bodySizeLimit no longer bites below the app's 25MB cap)"
  - "[x] If an upload fails for ANY reason, the recording is kept: the user sees the error with Retry and Download buttons — audio can never be lost"
  - "[x] Recordings use a speech bitrate so ~1h fits comfortably under the caps"
  - "[x] Permissions-Policy allows microphone for the site itself (recording works on Chrome/Android too)"
out_of_scope: []
code_anchors:
  - path: web/next.config.ts
    symbol: serverActions.bodySizeLimit + Permissions-Policy
  - path: web/app/_components/MeetingAttachments.tsx
    symbol: MeetingRecorder failure state
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1258
    started: 2026-07-16T12:58:32Z
    status: completed
    ended: 2026-07-16T13:03:40Z
    summary: "Long meeting recordings now upload — and even when an upload fails, the audio can never be lost again. What happened at your logistics meeting: the web framework had a hidden 2MB ceiling on uploads, so your two short test clips squeaked through while the real 45-50 minute iPhone recording (roughly 25-50MB as an m4a) was rejected with a generic error before the tool's own friendlier limit ever applied — and the recorder then threw the audio away. Four fixes: the transport ceiling is now 256MB and meeting attachments accept up to 250MB (about four and a half hours of iPhone audio — recordings are the one legitimately big meeting file, and storage is cheap); the recorder now HOLDS the finished recording until the upload succeeds, and on any failure shows the file size and error alongside \"Retry upload\" and \"Download recording\" buttons, so the worst case is saving it to the device; recordings ask the browser for a compact speech quality (phones that honour it produce ~12MB per hour); and a security header that unintentionally banned microphone use (which Chrome and Android phones enforce, unlike your iPhone) now permits it for the tool itself. The lost M-001 recording is unfortunately unrecoverable — this makes it the last one."
    test_plan: '1. Deploy, then on your iPhone open a test meeting and record ~3-5 minutes (a few MB) → Stop & attach → it uploads and appears in Attachments. 2. The real test at your next long meeting: a 45+ minute recording uploads (may take a moment on phone data — the button shows "Attaching recording…"). 3. Failure drill: turn on flight mode after stopping a recording → the error shows with the file size + "Retry upload" and "Download recording"; Download saves the audio to the device; turning data back on and pressing Retry attaches it. 4. On an Android/Chrome device, the Record button now works at all (previously blocked by the security header). 5. Server-side confirmation of the original failure (optional): sudo journalctl -u pm-tool --since "2 days ago" | grep -i "body exceeded" shows the old 2MB rejections. 6. Regression: normal document uploads on tickets unchanged (their 25MB cap untouched); transcribe-meetings picks the new recording up as usual.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
  - mobile
  - dogfood-finding
attention: null
version: 10
---

## Problem

Real loss at the route-planner logistics meeting (M-001): a short test recording uploaded, the actual (longer) meeting recording silently failed, a third short test uploaded — and the meeting audio is gone.

Root causes:
1. next.config serverActions bodySizeLimit is "2mb" — Next rejects bigger uploads before our action runs (short clips pass, real meetings don't). The app-level 25MB cap never gets a chance to speak.
2. The recorder discards the blob on upload failure — no retry, no download, audio unrecoverable.
3. Also found: the Permissions-Policy header sets microphone=() (deny all) — Safari doesn't enforce it, Chrome/Android would block recording entirely.

## Fix

- bodySizeLimit → 30mb so the friendly in-app 25MB cap is the real gate.
- Recorder keeps the audio on failure: clear error + "Retry upload" + "Download recording" (the audio can NEVER be lost again); shows the file size.
- Record at a voice bitrate (~32kbps opus) so an hour-long meeting is ~15MB.
- Permissions-Policy microphone=(self).

## Conversation

**2026-07-16 13:03 claude-code:** Run run-20260716-1258 completed — Long meeting recordings now upload — and even when an upload fails, the audio can never be lost again. What happened at your logistics meeting: the web framework had a hidden 2MB ceiling on uploads, so your two short test clips squeaked through while the real 45-50 minute iPhone recording (roughly 25-50MB as an m4a) was rejected with a generic error before the tool's own friendlier limit ever applied — and the recorder then threw the audio away. Four fixes: the transport ceiling is now 256MB and meeting attachments accept up to 250MB (about four and a half hours of iPhone audio — recordings are the one legitimately big meeting file, and storage is cheap); the recorder now HOLDS the finished recording until the upload succeeds, and on any failure shows the file size and error alongside "Retry upload" and "Download recording" buttons, so the worst case is saving it to the device; recordings ask the browser for a compact speech quality (phones that honour it produce ~12MB per hour); and a security header that unintentionally banned microphone use (which Chrome and Android phones enforce, unlike your iPhone) now permits it for the tool itself. The lost M-001 recording is unfortunately unrecoverable — this makes it the last one.

---

**2026-07-17 12:33 — you**

Done
