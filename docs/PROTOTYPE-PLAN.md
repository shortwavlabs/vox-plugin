# VOX — 48-hour feasibility and AFTERIMAGE decision plan

**Status:** instructions, not results. This timebox starts only when an agent begins the actual spike.

## Day 1 — Build sound, no product shell

1. Record source provenance (SHA-256, owner/license, channels, duration, sample rates); ignore all raw and rendered audio in git.
2. Implement offline slice editor metadata and deterministic waveform/source-region extraction. Initially use explicit manual marks; optional simple threshold/onset proposals. No automatic phoneme identification.
3. Implement B0 ordinary slice triggering, B1 equal-power fade, C1 FLOW legato voice overlap. Stereo, velocity, note-on/off, fixed MIDI event scripts. Basic MELODY via plain rate resampling; label pitch/time coupling.
4. Freeze test sources, transition parameters, loudness-match procedure, outcome measures and one-song MIDI phrase before listening.
5. Render with identical inputs, keys, phrase timing, musical dynamics and loudness; create blind random A/B/C packs plus preserved secret answer key.

## Day 2 — Blind audition, technical feasibility, decision

1. Owner auditions at least three test sets, preferably four phrases spanning sung/spoken/long-vowel/short rhythm. Seek independent second listener.
2. Score musicality, expressiveness, immediate utility, creativity, transition artifacts, and “I'd use this.” Inspect blend clicks and glottal/consonant duplication in the failure cases.
3. Compare best method with a conventional sampler where possible.
4. Measure peaks, nonfinite, processing cost estimate, memory caps and feasibility to port to JUCE under audio-thread constraints.
5. Write `docs/P0-FINAL-REPORT.md` **after** running: explicit PASS/PARTIAL/FAIL, exact reproducible hashes, blind results, CPU/memory evidence, objective failure modes and go/no-go. Do not convert an unlistened render into a musical PASS.

## Fair comparison against AFTERIMAGE

Score both candidates **independently** 1–5 on:
- Original/ownable central interaction
- Musical inspiration within first 30 seconds
- Audio quality and controllable expression
- Visual clarity and Blender feasibility
- Remaining product engineering / host risk
- Time left to qualify, sign and submit one entry

AFTERIMAGE has a C++ AU/VST3 development prototype and major offline validation, but an open small-buffer realtime benchmark gate and no C++ DAW listening yet. VOX initially has no engine and no host validation. Do not score a polished mockup as functional, and do not penalize VOX solely for lack of sunk-cost implementation. Explicitly distinguish **musical potential** from **deadline risk**.

## Hard stop

If FLOW is essentially a less capable Serato/Arcade/Quick Sampler crossfade and no distinct performance experience emerges, mark NO-GO. Avoid scope escalation into deep learning, source separation or formant-preserving synthesis. Keep research and prototype assets; resume AFTERIMAGE qualification if chosen.

## Deliverable contract

A short, skeptical final report, a blind-pack folder (ignored), deterministic scripts/tests with fixed seeds, source licenses, benchmark output, and one clear recommendation. **No major JUCE port or Blender modeling without an explicit subsequent approval.**
