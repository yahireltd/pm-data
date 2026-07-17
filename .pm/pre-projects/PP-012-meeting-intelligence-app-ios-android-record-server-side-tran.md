---
id: PP-012
slug: meeting-intelligence-app-ios-android-record-server-side-tran
title: Meeting Intelligence app (iOS + Android) — record → server-side transcribe + speaker ID → outcomes/tickets, as a standalone productizable module of pm-tool
state: draft
created: 2026-07-17T21:35:13Z
updated: 2026-07-17T21:35:13Z
stakeholders: []
source_tickets:
  - T-0614
  - T-0615
  - T-0620
summary: |-
  Turn the local meeting-transcription stack (T-0614 diarization, T-0615 voice enrolment, T-0620 ECAPA speaker separation — proven to tell apart sound-alike brothers) into a standalone mobile app that anyone on a team can use to capture meetings and have them become tracked work automatically. Vision (Austin, 2026-07-17): "it really can transform meetings — the modern dev's dream as a package."

  Core loop: phone app records a meeting → uploads audio → SERVER-SIDE transcription + diarization + speaker attribution (named via each person's voiceprint) → draft summary/outcomes → promote to pm-tool tickets/decisions with the right owners. Two personas already validated: Ben (lives in meetings → auto-captured into tracked work) and Austin (non-dev PM → meetings are where direction is set, so meeting→outcome→ticket is how "what we decided" reaches "what agents build").

  KEY ARCHITECTURE SHIFT to decide during planning: today the stack is deliberately LOCAL/OFFLINE (Austin's Mac; no cloud LLM; biometric voiceprints stay on-device). A phone app implies SERVER-SIDE compute (GPU box for whisperx+pyannote+ECAPA) and voiceprints living server-side — which raises real biometric-privacy/consent + multi-tenant-isolation questions that must be designed in, not bolted on. Decisions: (a) where transcription runs (dedicated GPU server vs on-device Core ML), (b) how voiceprints are stored/consented per person and per tenant, (c) cross-platform build (React Native fits Austin's RN dabbling, or native), (d) how it plugs into pm-tool (shared entity model, this becomes a first module of the multi-tenant/agents-as-team-members product vision), (e) transcription quality bar from the T-0620 findings (min ~1.5s windows, clean cross-condition enrolment, sequence smoothing, open-set threshold ~0.75 on ECAPA).

  Builds on the meeting-recording loop already live in pm-tool (browser record → S3 → transcribe-meetings worker → pm_attach_meeting_transcript → outcomes). The app is the mobile front of that same pipeline.
owner:
  kind: human
  name: Austin
version: 1
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
