---
id: T-0656
title: "Meeting recorder: crash-safe chunk persistence + recover-on-reload (don't lose a recording on battery death)"
type: feature
state: in_progress
created: 2026-07-23T15:44:53Z
updated: 2026-07-23T15:56:57Z
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
  - "[ ] While recording, each ~1s chunk is persisted to IndexedDB (verifiable in DevTools → Application → IndexedDB), keyed to the meeting + recording session, alongside the in-memory buffer"
  - '[ ] Kill the tab mid-recording (close tab / hard-reload to simulate a crash) and reopen the meeting page → a "Recover recording" prompt appears showing roughly how much was captured; accepting it rebuilds and uploads the partial recording, which then transcribes normally via transcribe-meetings'
  - "[ ] A clean \"Stop & attach\" still works exactly as before AND clears that session's IndexedDB chunks (no orphan-recovery prompt is offered afterwards)"
  - "[ ] A successful upload (normal or recovered) clears the stored chunks; a failed upload keeps them and still offers Retry + Download (existing T-0597 behaviour preserved)"
  - "[ ] IndexedDB write failure / QuotaExceeded degrades gracefully — recording continues from the in-memory buffer, a non-fatal warning is shown, no crash and no lost Stop"
  - "[ ] Works in Chrome + Safari (webm vs mp4) on desktop and iOS"
  - "[ ] Regression: normal meeting + ticket attachment upload / view / remove is unchanged"
out_of_scope:
  - Streaming chunks to the server mid-recording (separate, larger resilience ticket)
  - Cross-device / cross-browser recovery
  - Changes to the Mac transcription pipeline
code_anchors:
  - path: web/app/_components/MeetingAttachments.tsx
    symbol: MeetingRecorder (ondataavailable / onstop / start / stop / send)
  - path: web/app/_components/
    symbol: "NEW: small IndexedDB helper for recording chunk persistence (put/list/clear by session)"
relates:
  - T-0588
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260723-1556
    model: claude-opus-4-8
    started: 2026-07-23T15:56:57Z
    status: in_progress
labels:
  - meetings
  - comms
attention: null
version: 4
---

## Problem

The in-browser meeting recorder (T-0588) holds every audio chunk in **page memory** (`chunksRef`) and only assembles + uploads the file when **Stop** is pressed (`rec.onstop`). If the tab or device dies mid-recording — battery death, browser crash, accidental tab close — the whole in-memory buffer is gone: `onstop` never fires, nothing was ever persisted, nothing was uploaded.

This just cost a **real customer discovery session**: the Sophie sales interview (M-014, 23 Jul) was being recorded on a phone, the battery ran out mid-meeting, and the entire recording was lost. Austin + Ben had to reconstruct it from memory afterwards, and several of Sophie's answers now need re-asking.

The existing code comment `rec.start(1000); // gather in 1s chunks so a crash loses at most a second` is misleading — that only holds if the *recorder* stops cleanly. A whole-device power-off loses everything, because chunks are never flushed to durable storage.

## Context (current behaviour)

- `MeetingRecorder` in `web/app/_components/MeetingAttachments.tsx`.
- `rec.start(1000)` → `ondataavailable` fires ~every 1s and pushes each `Blob` into `chunksRef.current`.
- `rec.onstop` builds `new Blob(chunksRef.current)` and calls `upload()`.
- On a failed *upload* the finished file is already retained (`failedRef`) with Retry + Download (T-0597) — good. But there is no protection for a crash that happens **before** Stop.

## Design

1. **Persist chunks as they arrive.** In `ondataavailable`, in addition to pushing to `chunksRef`, append the chunk to **IndexedDB** keyed by a recording-session id (meetingId + startedAt + mime). Store enough metadata to rebuild the `File` later: `meetingId`, `project`, `mime`, `ext`, `startedAt`. Writes are async and must not block or drop chunks.
2. **Clear on success.** On a successful upload (normal Stop *or* a recovered one), delete that session's chunks from IndexedDB. Same on an explicit discard.
3. **Recover on reload.** On mount, look for an orphaned in-progress session for **this meeting** (chunks present, no clean-finish marker). If found, show a "Recover recording (~MM:SS captured)" prompt → rebuild the `Blob` from the stored chunks and run the existing `send()` path, reusing the failed-upload Retry + Download UX so a recovered partial can also be saved to device if upload fails.
4. A partial (up-to-battery-death) recording is a first-class win here — even a truncated m4a/webm transcribes fine.

## Out of scope (note as follow-ups)

- **Streaming chunks to the server mid-recording** (a stronger resilience layer that survives even losing the device entirely) — bigger, separate ticket.
- Cross-device / cross-browser recovery (IndexedDB is per-origin, per-device).
- Any change to the Mac transcription pipeline.

## Links

- Follows T-0588 (the recorder this hardens).
- Prompted by meeting M-014 (Sophie) — recording lost to battery death; see that meeting's failure-point outcome.
