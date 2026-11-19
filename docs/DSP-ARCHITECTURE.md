# VOX — Proposed instrument and DSP architecture

**Status:** NOT IMPLEMENTED OR MEASURED. Architectural RFC v0.1, not a validation report.

## 1. Basic topology

```text
Local file (user, non-RT) -> decode / validate -> channel normalize -> analysis
                                             |                |
                                             v                v
                                     immutable sample     candidate slices
                                             |                |
                                       patch generation <---- edit boundaries/UI
                                             |
                       MIDI note on/off -> bounded per-note voice allocator
                                             |
                         CHOP / MELODY / FLOW selection
                                             |
                           sample reads, pitch interpolation, gates
                                             |
                     transition engine / per-voice gain + envelopes
                                             |
                              stereo sum / output gain / meter
                                             |
                                        host output
```

**Instrument, not audio insert.** No obligatory sidechain, no assumption input buses exist. MIDI note events arrive with host sample offsets. Audio output supported for mono→mono and stereo→stereo instrument buses as exposed by the host; qualify layouts.

## 2. Suggested C++ boundaries

```text
plugin/
  CMakeLists.txt
  Source/
    PluginProcessor.{h,cpp}    // buses, MIDI/events, params, lifecycle and host state
    PluginEditor.{h,cpp}       // waveform, handles, key map; read-only snapshots
    dsp/
      ImmutableSample.{h,cpp}  // planar sample buffer, source rate, checksum
      SliceAnalysis.{h,cpp}    // OFF AUDIO THREAD: onset/silence proposals
      SliceMap.{h,cpp}         // sorted verified bounds & labels, immutable generation
      VoicePool.{h,cpp}        // bounded voices & stealing
      SampleRead.{h,cpp}       // interpolated mono/stereo reads, bounds checks
      ChopVoice.{h,cpp}       // MIDI note start / stop, envelopes
      FlowVoice.{h,cpp}       // optional C1/C2, tail hold + transition state
      RenderEngine.{h,cpp}    // sample by sample or block mixing, output meter
      PatchState.{h,cpp}      // versioned params, slice metadata, sample reference
    ui/
      WaveformView.{h,cpp}
      SliceEditor.{h,cpp}
      KeyboardMap.{h,cpp}
      ControlSprites.{h,cpp}  // JUCE overlays on static Blender assets
  Tests/
  Tools/
```

## 3. Offline file and slice path

- File decoding and statistics run on message/background thread, never audio callback; support WAV first, extensions only after verified JUCE codec support. If user loads 200MB/long file, enforce a bounded length and show a reason.
- Original recording remains unchanged. A new *immutable generation* owns decoded float PCM, slices, labels and analysis; the editor prepares/schedules a pointer-safe handoff. Old generations remain alive until all dependent voices exit; no memory reclamation within `processBlock`.
- Preferred internals: planar float buffers with source sample rate & channels, stable sample indices [start,end) into the original file, minimum slice duration floor, sorted boundaries, waveform peaks for display.
- Transient/silence detector suggests **candidate** cuts only (RMS envelope, spectral flux or onset strength). Manual edits always authoritative. “BA/AH/OO” in mockup are fictional example labels; no phoneme recognizer in MVP.
- Analysis should not accidentally chop all vowel formants at zero-crossings; test both short consonant and long vowel material. Keep waveform sample-accurate edit semantics.

## 4. Playback and pitch semantics

- MIDI triggers slice by fixed key mapping (configurable base note). Velocity mapped to explicit amplitude curve; note-off gate or one-shot according to selected mode. Handle overlapping note-ons, note-off IDs/order, sustain pedal only if intentionally supported, all-notes-off, panic, transport reset, bypass/host suspend.
- MELODY uses resampling rate `2^((note-root)/12) * sourceFs/outputFs`; note that **pitch and duration move together**, and vowel formants shift. Not independently pitch/time preserved.
- Use deterministic Catmull-Rom or higher-quality interpolation selected by measurement, compare passband/aliasing against a bandlimited resampler under 12–24 semitone excursions. Do not call interpolated resampling alias-free.
- Fixed polyphony cap (e.g. 12 or 16, pending measured performance), deterministic voice stealing with brief release, no unbounded stacks. Grain engines are **out of scope**.
- Envelope start/stop and boundary fades are sample-domain, not per-block. Playback including reversed or silent region (if supported) must never dereference outside sample.

## 5. FLOW transition contract (experimental)

- **B0** immediate retrigger; **B1** standard equal-power overlap as comparator.
- **C1** when a new note arrives while a prior voice's release tail is active, preserve selected previous segment's stable portion and crossfade to the next slice's onset over a bounded adjustable time. Avoid unintentional duplicate consonants. Explicitly decide whether FLOW is monophonic / last-note priority, or polyphonic per MIDI channel.
- Fades bounded e.g. 5–100 ms for prototype, nonzero even at 0% macro to avoid clicks. If two samples' spectra/pitches disagree, a crossfade may still sound unnatural. Do not make claims of “natural singing.”
- Extra analysis for C2 done OFF-THREAD only after C1 beats B1. Note lengths and overlap must remain related to MIDI gesture, not randomized.
- FLOW never silently changes slice identity or mapped note. UI shows actual transition region and live voice state.

## 6. Host and persistence

- Stable parameter IDs and versioned state. Non-continuous controls and keyswitch regions defined before shipping. No capture action restored from state.
- **Sample dependency strategy is an explicit unresolved design decision**. Option A: external file path + checksum with missing-file relink; option B: bundle compressed/PCM audio as host state with strict size bounds and privacy/DAW project-bloat tradeoff. Do not claim portable presets until selected and tested.
- Asynchronous load while audio plays: prepare new generation off callback, atomic/safe publish with bounded handoff, never block audio thread. Startup with missing file outputs silence with visible error. Closing editor cannot stop playback.
- MIDI event ordering and note boundaries must remain deterministic across varying blocks, host sample rates and offline bounce. Host automation and state restore test both AU and VST3.

## 7. Realtime contract

- No `new/delete`, locks, waiting, file I/O, codec decode, UI/Blender calls or logging in `processBlock`.
- Voice memory preallocated; source data immutable and lifetime-managed outside audio callback; bounded UI/audio FIFO or atomic snapshot with documented producer/consumer ownership.
- Profile per note count and all simultaneous voices during transitions at 44.1/48/88.2/96 kHz, blocks 1/16/32/64/128/512/1024, maximum velocity, mass note-offs, editor open/closed.
- Report median, p99, p99.9 and **max** callback times, CPU/overruns against host deadline, not average only. Explicit stop on any precommitted gate failure.
- Measure voice stealing artifacts, sums above 0 dBFS, and numerical safety. No secret limiter.

## 8. Qualification

1. Reference slice placement via exact MIDI sample times, including simultaneous notes and negative/zero sample edge.
2. Rate conversion, extreme low/high MIDI pitches, interpolation and expected pitch/duration drift.
3. Slice edit validation, empty/too-short input, source-file moves, missing/relinked audio, corrupted files and huge-file bounds.
4. Transparent CHOP baseline, loudness-matched FLOW comparisons, mono/stereo correlation and silence.
5. Deterministic render, finite samples, denormal decay, rapid note/parameter changes, repeated reset.
6. State: waveform/slices/parameters survive DAW save, reopen, bounce or have clearly documented external-file dependency.
7. pluginval and auval, real DAW auditions, long running stability, Windows native tests, licensing/asset audit before release.
8. UI: display actual waveform, selection-to-sample mapping, keyboard note response; Blender purely presentation, never required at runtime.

## 9. Open engineering questions

Host sample embedding; decoded length/size bound; root-note/channel layout; FLOW articulation; transition baseline thresholds; tempo handling (not required MVP); pitch/aliasing handling; exact JUCE version/pin; installer/signature policy; and the legal rights to every demo recording.
