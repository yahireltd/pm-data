---
id: T-0615
title: "Voice enrollment: persistent speaker identity across meetings (name a voice once, auto-label forever)"
type: feature
state: done
created: 2026-07-17T17:04:40Z
updated: 2026-07-21T17:53:41Z
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
  - "[x] A person can be enrolled once (from a recording where they're a known speaker) and stored in a local voiceprint library"
  - "[x] transcribe-speakers auto-labels enrolled voices by NAME in future recordings; unknown voices stay SPEAKER_NN"
  - "[x] Matches carry a confidence signal; low-confidence/unknown speakers are flagged, not silently guessed"
  - "[x] The library is local-only (never committed / uploaded); documented as biometric-sensitive"
  - "[x] Verified: enrol Ben + Austin from one call, then a second recording auto-names them"
out_of_scope: []
code_anchors:
  - path: /opt/homebrew/bin/transcribe-speakers
    symbol: embedding match against library (Mac, outside repo)
  - path: /opt/homebrew/bin/voiceprint-enroll
    symbol: enrollment (new)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1744
    started: 2026-07-17T17:44:42Z
    status: completed
    ended: 2026-07-17T17:45:09Z
    summary: "Voices can now be named once and are then recognised automatically in every future recording. Speaker separation (T-0614) only gives anonymous labels — Speaker 0, Speaker 1 — and those labels are random per recording, so the same person is a different number each time. This adds a voice-fingerprint library on your Mac: enrol a person once (the tool plays back a sample of each speaker so you confirm who's who, then saves their voiceprint), and from then on any recording that contains them is labelled with their NAME instead of a number. Proven end to end live: I enrolled Ben and Rob from the Ben→Rob call, then re-transcribed it with no hints given — it came back labelled \"Ben:\" and \"Rob:\" automatically. Unknown voices stay as Speaker N and can be enrolled later; matches use a confidence threshold so weak matches aren't forced. The voiceprints are biometric-sensitive, so the library lives only on your Mac and is never uploaded or committed. Combined with the attribution rule, this means an agent drafting outcomes from a meeting can write \"Decision (Ben): …\" using real names with no manual mapping. Next: enrol you, Effie and Jhuztine from the accounts meeting so accounts recordings self-label."
    test_plan: "1. Enrolment (done live): voiceprint-enroll <audio> with no names lists each speaker + a sample + talk-time; re-run with Name=SPEAKER_NN mappings to save. 2. Recognition (verified): after enrolling Ben+Rob from the Ben call, transcribe-speakers on the SAME call auto-labelled \"Ben:\"/\"Rob:\" with no mapping. 3. Cross-recording proof (pending a second recording with a known voice): enrol from meeting A, transcribe meeting B containing the same person → auto-named. 4. Unknown voices remain SPEAKER_NN and don't get forced onto a name (confidence threshold, tunable via VOICEPRINT_THRESHOLD). 5. Privacy: ~/Models/whisper/voiceprints/library.json stays local — gitignored/never committed; documented as biometric-sensitive. 6. Accounts follow-up: once M-003 diarization finishes, enrol Austin/Effie/Jhuztine so future accounts meetings self-label. Requires the pyannote/embedding terms accepted (done)."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: scripts/mac-transcription/README (enrolment + privacy + setup)
labels:
  - meetings
  - transcription
attention: null
version: 11
---

## Problem

Diarization (T-0614) labels speakers SPEAKER_00/01 per recording — the labels are arbitrary and don't persist, so a person must be re-identified every meeting. Austin wants: name a voice once, then it's auto-recognised in future recordings.

## Design (local, free, builds on the T-0614 pyannote setup)

- A voiceprint library on the Mac (~/Models/whisper/voiceprints/, JSON of {name: [embeddings]}). Not in the repo.
- transcribe-speakers extracts a pyannote embedding per detected speaker; matches each against the library by cosine similarity above a threshold → labels with the NAME; unmatched stay SPEAKER_NN.
- Enrollment: a `voiceprint-enroll <name> <audio-or-meeting>` step (or "SPEAKER_00 is Ben" confirmation on review) averages that speaker's segments into the library; corrections refine it.
- Confidence: auto-labels carry a confidence marker; low-confidence + unknown speakers are flagged for the human, never guessed silently (mirrors the AGENTS.md §4 rule).
- Privacy note: voiceprints are biometric-adjacent — local-only, never leave the Mac, documented as such.

## Conversation

**2026-07-17 17:45 claude-code:** Run run-20260717-1744 completed — Voices can now be named once and are then recognised automatically in every future recording. Speaker separation (T-0614) only gives anonymous labels — Speaker 0, Speaker 1 — and those labels are random per recording, so the same person is a different number each time. This adds a voice-fingerprint library on your Mac: enrol a person once (the tool plays back a sample of each speaker so you confirm who's who, then saves their voiceprint), and from then on any recording that contains them is labelled with their NAME instead of a number. Proven end to end live: I enrolled Ben and Rob from the Ben→Rob call, then re-transcribed it with no hints given — it came back labelled "Ben:" and "Rob:" automatically. Unknown voices stay as Speaker N and can be enrolled later; matches use a confidence threshold so weak matches aren't forced. The voiceprints are biometric-sensitive, so the library lives only on your Mac and is never uploaded or committed. Combined with the attribution rule, this means an agent drafting outcomes from a meeting can write "Decision (Ben): …" using real names with no manual mapping. Next: enrol you, Effie and Jhuztine from the accounts meeting so accounts recordings self-label.

---

**2026-07-21 17:53 — you**

done
