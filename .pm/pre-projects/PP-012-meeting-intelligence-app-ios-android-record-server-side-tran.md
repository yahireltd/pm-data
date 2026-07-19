---
id: PP-012
slug: meeting-intelligence-app-ios-android-record-server-side-tran
title: "The Meet Grinder — meeting intelligence: record → speaker-attributed transcript → decisions/tickets (iOS + Android app + agent MCP tools); flagship module of the pm-tool platform (PP-013)"
state: planning
created: 2026-07-17T21:35:13Z
updated: 2026-07-19T19:41:28Z
stakeholders: []
source_tickets:
  - T-0614
  - T-0615
  - T-0620
summary: |-
  Solve the MEETING problem specifically — and expose it two ways: (1) MCP TOOLS FOR AGENTS TO DIGEST meetings (first-class) — agents pull speaker-attributed transcripts, decisions and outcomes, so 'what was decided' flows straight into what agents build; and (2) a phone CAPTURE app (iOS/Android) for humans to record. Core loop: record → server-side transcribe + diarize + speaker-attribute (ECAPA voiceprints, per-segment, multi-condition + spoken self-introduction) → draft decisions/outcomes → tracked, owned tickets with the right owners.

  RELATIONSHIP (Austin, 2026-07-17): PP-012 and PP-013 are BOTH built, SHARING RESOURCES. PP-012 = the focused meeting capability + the agent-facing MCP surface. PP-013 = the white-label, multi-tenant productization of the whole tool. They share the transcription/diarization/speaker-ID pipeline and the server-side compute; PP-012's meeting intelligence is one flagship capability of the PP-013 platform.

  Proven today: ECAPA separates sound-alike siblings (0.90 same-voice vs 0.56 siblings) where off-the-shelf tools mislabel. Quality bar: >=1.5s windows, multi-condition enrolment, spoken self-introduction as top-priority identity source, calibrated open-set threshold, sequence smoothing.

  Shared architecture (with PP-013): server-side GPU compute; biometric voiceprint consent + per-tenant isolation; deploy model (isolated envs vs centralised multi-tenant DB). Builds on the meeting loop already live in pm-tool (browser record → S3 → transcribe worker → attach transcript → outcomes).
owner:
  kind: human
  name: Austin
version: 8
problems:
  - Meetings generate decisions and actions that evaporate — not captured as tracked work, so decisions are lost, work is repeated, and owners are unclear (the core org-coherence failure, at the meeting boundary).
  - Off-the-shelf transcription doesn't reliably attribute WHO said what — especially sound-alike voices — which is fatal for a PM tool whose job is 'who decided what'.
  - "The current stack is Mac-local and manual: only Austin can run it. It doesn't scale to a team, and it doesn't serve Ben, who spends most of his time in meetings."
  - Non-dev PMs set direction verbally; without a reliable meeting→outcome→ticket path, that direction never reaches the agents who build the work.
goals:
  - A phone app anyone on a team can use to record a meeting and have it become named, attributed, tracked work automatically.
  - Server-side transcription/diarization/speaker-ID so it works for a whole team, not just one person's Mac.
  - Attribution reliable enough to trust 'who decided', including sound-alike voices.
  - A standalone, productizable module of pm-tool — the first consumer-facing wedge of the multi-tenant, agents-as-teammates vision.
  - "WHITE-LABEL, production-ready pm-tool: the meeting app is the first wedge of a commercial product other teams can run under their own brand."
  - Make it easier to attribute by having each person SAY who they are the first time they speak (spoken self-introduction) — gives a clean in-condition anchor per meeting, auto-enriches enrolment, and seeds sibling-splitting.
scope_in:
  - "iOS + Android app: record a meeting, upload, review transcript + draft outcomes, promote to tickets/decisions."
  - "PER-DEVICE (MULTI-MIC) CAPTURE — the strongest attribution path: when attendees each run the app, every phone records its own owner's voice = one clean stream per person. Streams merged server-side with perfect per-speaker labels; no diarization, no sound-alike/sibling problem for anyone with the app. Single-device recording (with diarization + voiceprints) remains the fallback for guests without it."
  - "Server-side pipeline: transcription (whisper large-v3) + diarization (pyannote) + speaker attribution (ECAPA voiceprints, per-segment, multi-condition enrolment) — heavy compute off-device is the core differentiator; used for single-device capture and for un-enrolled guests."
  - Spoken self-introduction ('this is Ben') as a top-priority identity source, above voiceprints.
  - Per-person voice enrolment + training + consent; multi-condition prints that improve accuracy every meeting.
  - "Closed-loop integration with pm-tool entities: meeting → outcomes → tickets/decisions with the right owners."
  - Subscription tiers with compute-driven usage limits (audio minutes / file size / meetings per month) — transcription cost scales with minutes.
  - Biometric data handled with explicit consent and per-tenant isolation.
scope_out:
  - Real-time/live transcription (batch first).
  - On-device (Core ML) transcription — evaluate later; server-side first.
  - Languages beyond English in v1.
  - Full multi-tenant billing/SSO (arrives with the broader platform, not this module).
  - Video capture/analysis.
success_criteria:
  - On a real 3-person meeting including one sound-alike pair, speaker attribution is correct on the clear stretches and never confidently mislabels one person as another.
  - "End-to-end with no desktop: record on phone → a tracked ticket with the correct owner in a few minutes."
  - Works for a real team of enrolled members across different rooms/mics (multi-condition prints keep true-match scores clear of siblings).
  - Biometric voiceprints are stored only with explicit per-person consent and isolated per tenant; passes a privacy review.
cost_of_inaction: "\"Meeting decisions keep getting lost and re-litigated; Ben's meeting-heavy days produce zero tracked artifacts; Austin's verbal direction doesn't become buildable tickets. The org-coherence aim (aligning people, agents, projects, goals) stalls exactly where most decisions are actually made. And a genuinely differentiating capability — speaker attribution good enough to separate sound-alike siblings, already proven — never ships as a product.\""
milestones:
  - title: "Tech spike: production per-segment ECAPA speaker separation (largely de-risked 2026-07-17)"
    acceptance_criteria:
      - ECAPA embedding backend replaces the old pyannote embedding
      - Short segments merged into >=1.5s windows before embedding
      - Multi-condition enrolment + best-of-profiles matching
      - Spoken self-introduction parsed as top-priority identity source
      - Cluster-aware per-segment labelling (splits merged sibling clusters) + smoothing + calibrated open-set threshold
      - Proven on a real 3-person meeting including the Ben/Zac sibling case
  - title: "Server pipeline: transcription off the Mac onto a GPU service"
    acceptance_criteria:
      - whisper + pyannote + ECAPA run server-side as a callable service
      - Throughput adequate for a team's daily meeting volume (Ben is the stress test)
      - Shared with PP-013's platform compute
  - title: Agent-facing MCP surface — tools for agents to DIGEST meetings
    acceptance_criteria:
      - Agents can pull speaker-attributed transcripts, decisions and outcomes for a meeting via MCP
      - Decisions/outcomes are structured (who decided, owner, due) so agents can act on them
      - Extends today's pm_get_meeting / pm_record_outcome surface
  - title: USER VOICE TRAINING + enrolment + consent + privacy
    acceptance_criteria:
      - "Guided onboarding: read several VARIED phrases (not one) so the initial print is rich"
      - "Multi-condition training over time (different rooms/mics/phone) — the proven #1 accuracy lever that lifts true-match clear of sound-alikes"
      - A 'voice strength' meter + prompts to train more when a print is weak
      - "Passive training: every meeting AND every speaker-correction feeds back as a new labelled sample (accuracy compounds with use)"
      - Spoken self-introduction at meeting start tops up an in-condition sample automatically
      - Explicit biometric consent captured + revocable; per-tenant isolation of voiceprints; privacy review passed
  - title: Mobile app MVP (iOS + Android, one React Native codebase)
    acceptance_criteria:
      - "Home/Meetings: meetings grouped by day with status + decision counts; record hero button"
      - "Record (live): timer + waveform, 'in the room' attendee chips that light up as each voice is identified, spoken-intro prompt for unknown voices, pause/stop"
      - "Processing: upload + transcribe + identify-speakers progress; notify when ready"
      - "Meeting detail — Transcript: speaker-labelled, [MM:SS] timestamps, audio playback with tap-to-seek, tap a name to correct"
      - "Meeting detail — Outcomes: AI-drafted decisions + actions as cards (who decided / owner / due), Edit, Promote-to-ticket, Promote-all"
      - "Fix-speaker sheet: reassign this segment or all of a speaker; correction feeds back as a labelled voice sample"
      - "You: profile, your voiceprint with a VOICE-STRENGTH meter + 'train more' prompt, people & voices roster, plan + minutes-used meter"
      - "Enrol/train voice: read-aloud prompts, hold-to-record, explicit revocable consent"
      - "Offline capture: record without connection, auto-upload when back online"
  - title: Closed-loop pm-tool integration
    acceptance_criteria:
      - Meeting → outcomes → tickets/decisions with correct owners, end to end
      - Promote-to-ticket from the app creates a real pm-tool ticket with owner
---

# PP-012: Meeting Intelligence app (iOS + Android) — record → server-side transcribe + speaker ID → outcomes/tickets, as a standalone productizable module of pm-tool

## Summary

Turn the local meeting-transcription stack (T-0614 diarization, T-0615 voice enrolment, T-0620 ECAPA speaker separation — proven to tell apart sound-alike brothers) into a standalone mobile app that anyone on a team can use to capture meetings and have them become tracked work automatically. Vision (Austin, 2026-07-17): "it really can transform meetings — the modern dev's dream as a package."

Core loop: phone app records a meeting → uploads audio → SERVER-SIDE transcription + diarization + speaker attribution (named via each person's voiceprint) → draft summary/outcomes → promote to pm-tool tickets/decisions with the right owners. Two personas already validated: Ben (lives in meetings → auto-captured into tracked work) and Austin (non-dev PM → meetings are where direction is set, so meeting→outcome→ticket is how "what we decided" reaches "what agents build").

KEY ARCHITECTURE SHIFT to decide during planning: today the stack is deliberately LOCAL/OFFLINE (Austin's Mac; no cloud LLM; biometric voiceprints stay on-device). A phone app implies SERVER-SIDE compute (GPU box for whisperx+pyannote+ECAPA) and voiceprints living server-side — which raises real biometric-privacy/consent + multi-tenant-isolation questions that must be designed in, not bolted on. Decisions: (a) where transcription runs (dedicated GPU server vs on-device Core ML), (b) how voiceprints are stored/consented per person and per tenant, (c) cross-platform build (React Native fits Austin's RN dabbling, or native), (d) how it plugs into pm-tool (shared entity model, this becomes a first module of the multi-tenant/agents-as-team-members product vision), (e) transcription quality bar from the T-0620 findings (min ~1.5s windows, clean cross-condition enrolment, sequence smoothing, open-set threshold ~0.75 on ECAPA).

Builds on the meeting-recording loop already live in pm-tool (browser record → S3 → transcribe-meetings worker → pm_attach_meeting_transcript → outcomes). The app is the mobile front of that same pipeline.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
