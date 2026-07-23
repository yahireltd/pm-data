---
id: T-0656
title: "Meeting recorder: crash-safe chunk persistence + recover-on-reload (don't lose a recording on battery death)"
type: feature
state: review
created: 2026-07-23T15:44:53Z
updated: 2026-07-23T17:34:23Z
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
  - "[x] [ ] While recording, each ~1s chunk is persisted to IndexedDB (verifiable in DevTools → Application → IndexedDB), keyed to the meeting + recording session, alongside the in-memory buffer"
  - '[x] [ ] Kill the tab mid-recording (close tab / hard-reload to simulate a crash) and reopen the meeting page → a "Recover recording" prompt appears showing roughly how much was captured; accepting it rebuilds and uploads the partial recording, which then transcribes normally via transcribe-meetings'
  - "[x] [ ] A clean \"Stop & attach\" still works exactly as before AND clears that session's IndexedDB chunks (no orphan-recovery prompt is offered afterwards)"
  - "[x] [ ] A successful upload (normal or recovered) clears the stored chunks; a failed upload keeps them and still offers Retry + Download (existing T-0597 behaviour preserved)"
  - "[x] [ ] IndexedDB write failure / QuotaExceeded degrades gracefully — recording continues from the in-memory buffer, a non-fatal warning is shown, no crash and no lost Stop"
  - "[x] [ ] Works in Chrome + Safari (webm vs mp4) on desktop and iOS"
  - "[x] [ ] Regression: normal meeting + ticket attachment upload / view / remove is unchanged"
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
    status: completed
    progress:
      - at: 2026-07-23T16:01:36Z
        note: 'Implemented crash-safe recording: new IndexedDB helper (recordingStore.ts) persists each ~1s chunk; MeetingRecorder opens a session on start, flushes chunks, clears on successful upload, and offers a "Recover & attach" prompt on reload for any orphaned recording. Typecheck + production build both pass. About to commit + push (Austin deploys).'
    ended: 2026-07-23T16:02:33Z
    summary: Meeting recordings can no longer be wiped by a dead battery or a crash mid-meeting. Before this, the recorder kept the whole recording in the page's memory and only saved it to the server when you pressed "Stop & attach" — so if the phone died or the tab closed during the meeting, the entire recording was lost (exactly what happened with the Sophie sales session). Now, while you record, the audio is continuously saved to the device's local storage a second at a time. If the recording is interrupted before you press Stop, the next time you open that meeting the tool shows an "unfinished recording found — Recover & attach" prompt, and one click uploads everything that was captured up to the moment it was cut off. That partial recording transcribes normally, so you'd keep almost the whole meeting instead of nothing. A normal Stop still works exactly as before and cleans up the local copy; if local saving isn't available for any reason, recording still works and just shows a small "keep this tab open" warning rather than failing. If we'd done nothing, every recording would stay one dead battery away from being lost — a real risk for the meeting app.
    test_plan: |-
      Deploy first, then on a meeting page (Attachments area):

      HAPPY PATH (recovery)
      1. Click "Record meeting", speak for ~20–30s. In DevTools → Application → IndexedDB → "pm-meeting-recordings", confirm a `sessions` row and growing `chunks` rows appear while recording.
      2. Simulate a crash: WITHOUT pressing Stop, close the tab (or hard-reload the page). Reopen the same meeting.
      3. Expect a banner: "Unfinished recording found (~MM:SS) …" with "Recover & attach" + "Discard". Click Recover & attach → it uploads; the meeting gets a `meeting-recording-….m4a/webm` attachment that plays back. Run `transcribe-meetings` on the Mac → a transcript lands (a truncated file transcribes fine).

      NORMAL PATH (unchanged)
      4. Record and click "Stop & attach" as usual → attaches and plays back. Reopen the meeting → NO recovery banner (the session was cleared on success). Check IndexedDB → the session's chunks are gone.

      DISCARD
      5. Repeat step 1–2 to get the banner, then click "Discard" → banner clears, IndexedDB session removed, no attachment created.

      EDGE CASES
      6. Failed upload still safe (T-0597 preserved): if an upload fails, you still get Retry + Download, and the recording isn't lost. A recovered recording that fails to upload should also land in that Retry/Download state.
      7. Graceful degrade: in a context without IndexedDB (or private-mode quota block), recording still works from memory and shows the small "Can't save a local backup — keep this tab open" note; Stop & attach still uploads.
      8. Browsers: verify Chrome (webm) and Safari incl. iOS (mp4) both record, persist, and recover.

      CROSS-IMPACT
      9. MeetingRecorder is the only changed component; regression-check normal meeting attachment upload/view/remove AND ticket attachments (shared AttachmentsPanel) still work. No server/MCP changes — the /api upload route and transcription worker are untouched.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
  - comms
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-23T16:02:33Z
version: 13
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

## Conversation

**2026-07-23 16:02 claude-code:** Run run-20260723-1556 completed — Meeting recordings can no longer be wiped by a dead battery or a crash mid-meeting. Before this, the recorder kept the whole recording in the page's memory and only saved it to the server when you pressed "Stop & attach" — so if the phone died or the tab closed during the meeting, the entire recording was lost (exactly what happened with the Sophie sales session). Now, while you record, the audio is continuously saved to the device's local storage a second at a time. If the recording is interrupted before you press Stop, the next time you open that meeting the tool shows an "unfinished recording found — Recover & attach" prompt, and one click uploads everything that was captured up to the moment it was cut off. That partial recording transcribes normally, so you'd keep almost the whole meeting instead of nothing. A normal Stop still works exactly as before and cleans up the local copy; if local saving isn't available for any reason, recording still works and just shows a small "keep this tab open" warning rather than failing. If we'd done nothing, every recording would stay one dead battery away from being lost — a real risk for the meeting app.
