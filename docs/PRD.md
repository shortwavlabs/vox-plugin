# VOX — Product Requirements (concept v0.1)

**Status:** proposal, no working software or passed audio gates. **Product:** Shortwav Labs VOX (working name; trademark search required). **Purpose:** 48-hour challenger to AFTERIMAGE for The Audio Programmer Plugin Jam 2026–27. **Decision deadline:** 48 hours of focused prototyping after implementation begins, not permission to spend 48 hours polishing a UI.

## Problem and audience

Electronic, pop, hip-hop and experimental producers transform found/recorded vocals into hooks. Current samplers already automate slices and MIDI mapping. The friction worth exploring is *playing articulate, connected phrases* from raw recordings without editing each inter-syllable transition in a DAW.

The target user can supply a rights-cleared vocal recording, play a MIDI keyboard, and wants a musical result within 30 seconds. Avoid claiming the market is underserved merely because a forum post requests a vocal slicer; see [market research](MARKET-RESEARCH.md).

## Value proposition / differentiated hypothesis

**Drop a vocal. Discover editable fragments. Play them as phrases.**

The proposed `FLOW` mode preserves audible vowel tails and smooths the handoff to a new fragment under explicit MIDI control, making a phrase feel connected rather than a sequence of one-shot retriggers. This *may not* be better than a good conventional sampler; the blinded experiment determines that.

**Not** automatic lyric interpretation, speech synthesis, AI singing, full phoneme recognition, source separation, or a Kontakt/Serato/Arcade replacement.

## User journey

1. Load a local mono/stereo WAV or AIFF/FLAC (formats are proposed; probe actual decoder availability before implementation). Reject oversized/invalid files with understandable errors.
2. See a real waveform and automatic *candidate* cut boundaries (energy/onset/silence heuristics), inspect and drag boundaries. Labels such as “AH / EH” are editable human labels, **never unverified model transcripts**.
3. Trigger up to 16 fragments chromatically or via mapped pads. A visible key map and incoming MIDI event state make routing obvious.
4. Switch CHOP / MELODY / experimental FLOW, compare against raw slices at matched loudness. Audition a short riff immediately.
5. Save a patch with stable slice/parameter state and explicit sample-source dependency behavior; no silent reference to a missing file.

## Modes

| Mode | Musical contract | Proof threshold |
|---|---|---|
| **CHOP** | 1:1 MIDI-to-slice triggers with gated/one-shot playback, envelope and start/end edits | stable, fast, musical baseline; never claimed novel |
| **MELODY** | one selected region mapped across semitones using simple resampling in P0 | pitch relative to MIDI is correct; tempo and formant move together by design |
| **FLOW** | legato note-to-note handoff between distinct regions; short overlap/phase-appropriate amplitude envelopes, optional tail continuity | blind comparisons prefer FLOW on at least two different phrases, without unacceptable clicks or smears |

FLOW is *experimental*, and can be removed. The words “phoneme-aware” / “natural singing” are prohibited marketing claims until objectively established. If all it does is equal-power crossfade between samples, describe it that way.

## Jam MVP (only after P0 go)

- macOS AU/VST3 instrument and Windows VST3 after testing; JUCE 9/C++20/CMake; AGPL-3.0-only.
- One sample per patch, 16 editable slices maximum, MIDI note on/off/velocity, mono/stereo playback, 8–16 voices with fixed ceiling, CHOP and MELODY.
- FLOW only if distinct and stable; editor waveform, key map, 3 performance macros, envelope, output gain, safe load/reload.
- Host sample rates 44.1/48/88.2/96 kHz; correct sample-rate conversion, stable state restore/automation, sensible missing-file message.
- No bundled unlicensed vocal content, cloud calls, AI transcription, stem separation, Kontakt/NKI import, complex time-stretch, multi-file multisampling, phrase generation, or premium post processing.

## Product-level success gates

- A new user can load a vocal and make a 4-note performance in ≤30 s on a verified test session.
- Four test files: dry sung phrase, spoken phrase, sustained vowel and rhythmic chops, with lawful usage; demonstrate useful results on ≥2.
- At least two independent blind listeners including owner prefer FLOW over ordinary CHOP or a reference sampler under loudness-matched conditions for ≥2 files. With small samples, describe as directional not statistically significant.
- Audio: no nonfinite, no stuck notes, no audible uncommanded clicks, realistic voice ceiling, repeatable render.
- AU/VST3 plugin validation, host state save/reload, asset relocation, closed-editor playback, resampling and CPU budget checked before shipping.
- 30 s demo sells musical result, 3 min video communicates workflow; adhere to current contest rules.

## GO / NO-GO after 48 hours

**GO** only if blind listening finds a differentiating musical FLOW behavior; rights are known for demo audio; a minimal C++ port is architecturally plausible; and project schedule leaves adequate qualification/UI time.

**NO-GO / defer** if CHOP is the only engaging behavior, FLOW is indistinguishable from conventional retrigger/crossfade, sound quality demands unbounded advanced time-stretch, or sample/host persistence makes deadline implausible. AFTERIMAGE remains the alternative, though its realtime gate is open.

## Open decisions

VOX name clearance; input format and maximum duration; user-file embedding vs explicit external-reference strategy; polyphony and key range; default labels; window sizes/voice count; reference baseline; sound and acquisition rights. All open, not silently decided.
