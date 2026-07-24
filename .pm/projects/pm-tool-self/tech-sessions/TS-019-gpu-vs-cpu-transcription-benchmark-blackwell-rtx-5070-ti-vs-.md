---
id: TS-019
slug: gpu-vs-cpu-transcription-benchmark-blackwell-rtx-5070-ti-vs-
title: GPU vs CPU transcription benchmark — Blackwell (RTX 5070 Ti) vs Ryzen 9 7900X on the meeting pipeline
project: pm-tool-self
created: 2026-07-24T00:49:14Z
updated: 2026-07-24T01:40:10Z
decisions:
  - "RESULTS (M-013, 1386s audio). GPU RTX 5070 Ti: transcribe 24.2s (57× realtime); full transcribe+align+diarize 40.8s (34× realtime, diarization adds only ~17s); ECAPA labeling of 257 segments 3.3s (420×); peak VRAM 7.4GB — fits alongside other work on a 16GB card. CPU Ryzen 9 7900X (int8): transcribe 404s (3.4×); full pipeline 1088s (18m08s, 1.27×); ECAPA 11.2s. Bottom line: GPU full pipeline ≈44s vs CPU ≈18min vs Mac observed ≈82min — GPU is ~25× the strong desktop CPU and ~112× the current Mac experience. A 23-min meeting is transcribed, diarized and speaker-named in under a minute; a full 8-hour day of meeting audio ≈ 15 min of GPU time. VERDICT: a Blackwell-class GPU is decisively worth it for PP-012's meeting pipeline, and its throughput underwrites PP-013's per-tenant compute economics."
  - "Environment/repro notes for whoever builds the production GPU worker: (1) Blackwell (sm_120) needs torch cu128 builds — torch 2.8.0+cu128 with ctranslate2 4.8.1 works; the Mac's torch==2.5.1 pin has no Blackwell support, and the pyannote weights_only issue that forced that pin is fixed in pyannote.audio 4.x. (2) Newer whisperx defaults diarization to pyannote/speaker-diarization-community-1 — gated SEPARATELY from the 3.1-era models; each HF account must accept its terms (Austin hit this). (3) Check nvidia-smi before benchmarking/running on shared boxes — first GPU runs OOM'd because ollama held a 14GB model resident. (4) The T-0658 file-handoff channel moved the code bundle (83MB), M-013 audio and diarize cache between agent sessions for this work; voiceprints were deliberately NOT transferred — Ben/Austin/Zsolt were re-enrolled locally from the M-013 recording (104/42/25 windows), which is the right biometric practice for cross-machine setup."
  - "METRIC CLARIFICATION (Austin's catch — the two \"×\" figures are different ratios and must not be mixed). \"Realtime factor\" = audio duration ÷ processing time (can it keep up with a day of meetings); \"GPU-vs-CPU speedup\" = CPU time ÷ GPU time. Clean table for the M-013 recording (1386s): transcribe — GPU 24.2s vs CPU 404s → speedup 16.7×, GPU realtime 57×; transcribe+align+diarize — GPU 40.8s vs CPU 1088s → speedup 26.7×, GPU realtime 34×; ECAPA labeling — GPU 3.3s vs CPU 11.2s → speedup only 3.4× (GPU realtime 420×, CPU realtime 124×). The ECAPA stage is cheap on both devices — much of its wall-clock is Python overhead and audio slicing, not neural compute — so it was never the bottleneck. The stages where the GPU matters are transcription and above all diarization (CPU ~11min → GPU ~17s), which dominate the totals: full pipeline ~44s GPU vs ~18min CPU ≈ 25× end-to-end."
  - "FINAL RESOLVED TABLE (after Austin's cache-suspicion flag was investigated with disk evidence — the GPU full-pipeline run was confirmed fresh: its diarize stage wrote its own 238-segment output with transcription text at 7.3GB peak VRAM, vs the 257-segment Mac cache; the cache was only used in the earlier separately-reported pre-token ECAPA timings). Full FRESH pipeline (transcribe+align+diarize+ECAPA), same M-013 recording, 1386.6s: RTX 5070 Ti **44.1s (31× realtime)** · Ryzen 9 7900X ~1099s / 18m19s (1.26×) · **M2 Mac mini 1780s / 29m40s (0.78× — BELOW realtime)**. GPU = 40.4× the M2, 25× the Ryzen. Mac transcribe-only 253s was whisper.cpp (engine differs from the faster-whisper rows — full-pipeline row is the clean comparison); Mac diarize = pyannote-3.1 vs GPU community-1 (same work class, not identical weights). The previously-quoted \"~82min Mac\" is M-013's real user-experienced upload→transcript latency that day (includes queue wait behind other recordings), vs 1780s pure compute — both belong in the record with those labels. VERDICT UNCHANGED AND STRENGTHENED: the M2 cannot keep up with meeting volume (sub-realtime); a Blackwell-class GPU makes the pipeline effectively instant (44s/meeting) — decisively worth it."
actions:
  - text: Decide the work-machine purchase (Blackwell GPU) with these numbers; if bought, port the transcribe-meetings worker to the GPU box using the bundle's tool-integration notes and this session's environment notes.
side_quests: []
problem: "Deciding whether a Blackwell-GPU work machine is worth buying for the meeting-intelligence pipeline (PP-012 / PP-013): the current Mac mini worker took ~82 minutes observed (upload 12:59 → transcript 14:21) to process the 23-minute M-013 IT Meeting. We benchmarked the full pipeline — whisper large-v3 transcribe, pyannote diarization, ECAPA speaker labeling — on Austin's home GPU box (RTX 5070 Ti 16GB, Ryzen 9 7900X, 187GB RAM), same audio, GPU vs CPU."
success_criterion: Hard per-stage timings for GPU (fp16) and CPU (int8, Mac-equivalent) on the real M-013 recording, giving a data-backed answer to "is a Blackwell work machine worth it".
version: 5
---

# TS-019: GPU vs CPU transcription benchmark — Blackwell (RTX 5070 Ti) vs Ryzen 9 7900X on the meeting pipeline

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
