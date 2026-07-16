---
id: T-0597
title: Long meeting recordings fail to upload and the audio is lost (2MB action limit + no retry/download)
type: bug
state: in_progress
created: 2026-07-16T12:58:08Z
updated: 2026-07-16T12:58:32Z
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
  - A 10+MB recording uploads successfully (bodySizeLimit no longer bites below the app's 25MB cap)
  - "If an upload fails for ANY reason, the recording is kept: the user sees the error with Retry and Download buttons — audio can never be lost"
  - Recordings use a speech bitrate so ~1h fits comfortably under the caps
  - Permissions-Policy allows microphone for the site itself (recording works on Chrome/Android too)
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
    status: in_progress
labels:
  - meetings
  - mobile
  - dogfood-finding
attention: null
version: 3
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
