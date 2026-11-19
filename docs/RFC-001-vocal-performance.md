# RFC-001 — Is a connected vocal instrument musically better than slice triggering?

**Status:** PROPOSED / FALSIFIABLE. Date: 2026-11-18. No code, renders, auditions or passed tests.

## Context

Sampler auto-chop and keyboard maps are mainstream. Serato Sample, Arcade and Logic Quick Sampler already cover these affordances. VOX should be judged on a *single distinguishable transformation*: playable connection between user-selected vocal fragments.

## Hypotheses

- **H1:** On sung and spoken phrases, a transition that retains a source vowel tail while introducing the next onset yields a connected phrase perceived as more musical than simply retriggering or fading unrelated samples.
- **H2:** The transition improves playability under real note-off, legato, retrigger and velocity variation, not only on one rendered MIDI script.
- **H3:** This can be implemented in bounded, polyphonic JUCE C++ without relying on an external model/service or expensive pitch/time decoupling.
- **H4:** A restrained visible handoff control makes the outcome understandable in 30 seconds.

## Competing implementations (equal-conditions)

**B0 — Naive slices:** user-selected/onset-edited regions; retrigger sample with 5–10 ms envelope; pitch by resampling. This is a baseline, not a straw man.

**B1 — Standard crossfade:** equal-power tail of outgoing voice to incoming new slice with specified overlap, no special continuity. This is the *real reference*.

**C1 — FLOW tail hold:** use note-off/legato events to keep a previous sustained-vowel portion under a deterministic release, start the next transient, and fade based on both regions' envelope/energy. Still simple, honest DSP. No phoneme recognition.

**C2 — FLOW transition-aware scheduling:** detect speech-energy/onset boundaries offline; choose a safe release zone and overlap based on measured attack, RMS and zero-crossing cues. Defer if it confounds the blind test or requires bespoke analysis.

**Optional fallback:** adjacent-slice micro-looped vowel sustain with crossfades; requires its own tone/aliasing gate, and is not automatically in the 48 h plan.

## Prototype corpus

At least 4 **local, correctly licensed** phrases: dry sung rhythmic phrase, legato singing, spoken short phrase, sustained vowel. Mono and stereo; actual musical variations; annotate sample rate, duration, rights, hash, segment boundary edits. Audio ignored by git. Do not publish a render if any underlying license disallows it.

## Trial design

Day 1: testable Python or C++ offline engine, manual boundaries + simple energy/onset proposals. Fix MIDI pattern and source selection. Render B0, B1 and C1 (optionally C2); deterministic random seed, same envelope targets, note velocities and output reference loudness. Include silence, clipped consonant and long vowel control cases. Do **not** define acceptance thresholds after hearing outputs.

Day 2: create randomized blind packs of B0/B1/C1, include an ordinary sampler reference where feasible; write an independent answer key; ask owner to rank musicality, phrase continuity, transients, pitch quality and “would you play this?” without opening key. Prefer multiple musical MIDI patterns so overfitting a demo is evident.

Must report failures: repeated breath, clicks, onset smearing, strange formants, aliasing at transposition, unnatural overlap of two syllables, unwanted reverb-like wash, loudness bias and silent/ambiguous auto-slices.

## Technical gates (proposal; freeze before run)

1. Every render finite, no buffer overrun; deterministic reference outputs.
2. CHOP baseline plays slice starts within ±1 sample from the timestamped MIDI events in offline sample-domain implementation.
3. Silent regions handled/rejected explicitly; no negative-index wrap.
4. No 0 dBFS guarantee promised for summed voices; measure peaks and crest; display headroom warnings.
5. Audible transition click/defect listening plus documented transient/first-difference metrics, not metrics alone.
6. In bounded realtime feasibility estimate: memory = file size + analysis + fixed number of voices; computational cost roughly voices × interpolation taps per output sample; no audio-thread allocation or file decoding.
7. Qualitative differentiation: owner and another listener independently prefer C1/C2 to B1 across 2+ phrases with documented descriptions; if not, NO-GO.

## Decision and next step

Capture exact hashes and results in `docs/P0-FINAL-REPORT.md` only after experiments. **No JUCE, Blender production or GitHub release before GO**. Compare winning VOX *musicality, uniqueness, feasibility, time left* with AFTERIMAGE; do not favor AFTERIMAGE because of sunk development cost nor VOX because a static mockup looks attractive.

## Anti-scope-creep

No automatic transcription, neural source separation, formant correction, model downloads, stem extractor, convolution, sample library browser or 3D rendering in the spike. Distinguish a conceptual demo from a tested engine.
