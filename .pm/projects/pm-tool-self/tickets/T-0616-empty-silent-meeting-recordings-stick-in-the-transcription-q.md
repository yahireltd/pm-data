---
id: T-0616
title: Empty/silent meeting recordings stick in the transcription queue and retry forever
type: bug
state: done
created: 2026-07-17T19:27:11Z
updated: 2026-07-21T17:31:30Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A silent/empty recording dequeues with a clear 'no audible speech' note instead of erroring forever"
  - "[x] A genuine transcription crash (non-zero exit) still stays queued for retry"
  - "[x] Follow-up noted: the Record button should guard against uploading empty/near-empty recordings"
out_of_scope: []
code_anchors:
  - path: mcp-server/scripts/transcribe-meetings.ts
    symbol: empty-transcript handling
  - path: web/app/_components/MeetingAttachments.tsx
    symbol: MeetingRecorder empty-blob guard (follow-up)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1927
    started: 2026-07-17T19:27:35Z
    status: completed
    ended: 2026-07-17T19:27:52Z
    summary: "A broken or silent meeting recording no longer jams the transcription queue. While testing the real end-to-end process, the worker found a recording on a sales meeting that was only 975 bytes — effectively an empty file from a Record button that fired and stopped with nothing captured. Transcription rightly found no words, but the old behaviour treated that as a failure and left the recording in the queue, so it would have failed again on every future run — quietly clogging the pipeline. Now, when transcription runs successfully but finds no speech, the tool attaches a short note (\"no audible speech in this recording\") to the meeting so the item clears the queue; a real transcription crash still stays queued to retry later. One follow-up noted for later: the Record button itself should refuse to upload an empty recording so duds never reach the queue in the first place. Benefit: one bad recording can't silently block everyone else's transcripts."
    test_plan: '1. Verified live: the stuck 975-byte M-010 recording now dequeues — transcribe-meetings attached a "no audible speech" note and the next run reported "queue empty". 2. A normal recording with speech still transcribes and attaches as before (no regression — the empty path only triggers on a zero-length transcript from a successful run). 3. Follow-up (separate): record button empty-blob guard on the client. NOTE: the commit message mislabels this as T-0599 (id guessed before the ticket existed) — the real ticket is T-0616; not worth a force-push.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
  - transcription
  - dogfood-finding
attention: null
version: 9
---

## Problem

Testing the real transcription process, the worker found a queued M-010 recording (975-byte webm — a mis-fired Record button: valid opus header, zero audio frames). Transcription correctly produced no text, but the worker THREW ("transcription produced no text") and left it in the queue — so it failed on every subsequent run, clogging the queue indefinitely.

## Fix (done)

transcribe-meetings: an empty transcript from a SUCCESSFUL pipeline run (exit 0 — no speech) now attaches a short "no audible speech" note so the recording dequeues; a genuine pipeline crash (non-zero exit) still errors and stays queued for retry.

## Related gap (follow-up)

The Record button uploaded a 975-byte empty recording as if valid. Client-side guard: refuse/​warn on a near-empty blob (e.g. < a few KB or < ~1s) so duds don't reach the queue at all.

## Conversation

**2026-07-17 19:27 claude-code:** Run run-20260717-1927 completed — A broken or silent meeting recording no longer jams the transcription queue. While testing the real end-to-end process, the worker found a recording on a sales meeting that was only 975 bytes — effectively an empty file from a Record button that fired and stopped with nothing captured. Transcription rightly found no words, but the old behaviour treated that as a failure and left the recording in the queue, so it would have failed again on every future run — quietly clogging the pipeline. Now, when transcription runs successfully but finds no speech, the tool attaches a short note ("no audible speech in this recording") to the meeting so the item clears the queue; a real transcription crash still stays queued to retry later. One follow-up noted for later: the Record button itself should refuse to upload an empty recording so duds never reach the queue in the first place. Benefit: one bad recording can't silently block everyone else's transcripts.

---

**2026-07-21 17:31 — you**

done
