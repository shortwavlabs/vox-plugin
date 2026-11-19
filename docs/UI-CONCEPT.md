# VOX — Visual identity and Blender reconstruction guide (v0.1)

**Status:** design concept, not an implemented editor. See [editable SVG front-panel reference](ui/vox-front-panel.svg). A separate realistic generated visual may be supplied in the conversation; the SVG is the canonical **layout** guide, not a promise of mechanically exact knobs.

## Aesthetic concept

A premium dark-brushed-metal vocal instrument somewhere between boutique 1970s studio hardware and modern electronic performance equipment. Charcoal housing, muted bronze/copper machined perimeter, tiny screws, anti-reflection black glass over real waveform, turquoise for slice A and warm amber for alternative/selected slice, soft stage lighting, subtle wear only in physical chassis—not legibility-critical UI.

**Proposed colors:** charcoal `#171a1b`, glass `#090e11`, bronze `#b5845c`, amber `#e8a661`, teal `#5fdbcd`, off-white `#ebe2d3`. Use actual texture tests and verify accessible contrast. Use legally redistributable fonts.

## Logical layout (1600 × 1000 reference coordinates)

1. **Header 0–110:** VOX logotype center; Shortwav Labs left; instrument descriptor right. Do not assume copyright to a specific branded wordmark asset.
2. **Screen 112–470:** large real waveform stage. Left menu for patch name, MAP / SLICE / FX; central 8–16 user-editable vertical slice regions, handles, timestamps; right sample metadata and IMPORT. Onset markers are visually and semantically distinct from slice labels. “BA / AH / OO / EH” are **demonstration labels set by a human**, not speech recognizer output.
3. **Performance deck 480–740:** three tactile buttons CHOP / MELODY / FLOW; one large central TRANSITION knob; envelope, pitch and output knobs with deterministic indicator angle; real status below. Make FLOW clearly experimental in development builds.
4. **Keyboard 742–986:** accurate two-octave mapping and pressed-note feedback. Control notes, octave labels, no misleading MIDI response if the input is silent.
5. **Responsive / resizing:** keep waveform and slice handles interactive at minimum 1024 × 640 logical; layout transforms together; no tiny hidden controls.

## 3D asset strategy

Blender outputs **static chassis + physically rendered control sprites**, JUCE draws dynamic waveform and interactive overlays. No embedded 3D/WebView required.

Proposed collections:
```
01_CHASSIS_STATIC      enclosure, bronze bezel, screws, panel engraving, keyboard tray
02_GLASS              black display glass, edge seal, reflections, screen mask
03_CONTROLS           large/small rotary knobs, three mode buttons, cap/press states
04_KEYS               white/black keys, at-rest and pressed variants
05_LIGHTING_CAMERA    locked orthographic camera, softboxes, filmic color
06_EXPORT_HELPERS     alpha/mask/pivots/registration
```

Candidate exports:
```
assets/blender/vox-panel.blend
assets/ui/panel/enclosure@2x.png
assets/ui/panel/screen-frame@2x.png
assets/ui/controls/knob-large@2x.png
assets/ui/controls/knob-small@2x.png
assets/ui/controls/mode-normal@2x.png
assets/ui/controls/mode-active@2x.png
assets/ui/keys/key-up@2x.png
assets/ui/keys/key-down@2x.png
assets/ui/layout.json
```

- Fix physical aspect ratio/camera/lighting before render, export at 1× and 2×. Define per-element rectangles and rotational pivots in pixel and JUCE logical units; include a reference transparency checker.
- Composite static art behind JUCE-drawn waveforms, slice markers, labels, meters and MIDI indicators. Actual PCM and slice state drive dynamic components; no fake scrolling/recording waveform.
- Avoid baked-in labels for dynamic settings, patch name or source metadata. Accessible JUCE text and hit targets matter more than photo realism. Use sRGB export with edge alpha verification.
- Keep sample controls independent of Blender art and provide a stripped-down JUCE editor fallback.
- No expensive real-time Blender rendering. Avoid proprietary fonts/texture packs without rights.

## Interaction and test gates

Drop zone & file picker; immediately report decode failures, truncation and source location. Slice handles snap to sample-domain positions and show selection length, voice MIDI note, and audition state. Mode switches must alter audio, not just glow. Attack/release and transition controls map monotonically to DSP, precisely; MIDI activity shown only for actual received messages.

Keyboard: velocity/pressed highlights from host MIDI; supports mouse preview only with explicit note lifecycle and no stuck voices. In FLOW transitions, optional tail overlay labels the actual playing voices and their relative amplitudes.

**UI success:** new user can understand where to load, edit and trigger slices within 30 seconds. The screen dominates visually but doesn't obscure playable controls. Blender reconstruction must retain usable low-density typography at typical 100% and Retina scale.

## Concept-to-production hazards

- AI/3D concept imagery often shows keyboard geometry and labels inconsistently; correct against a canonical 2D grid before modeling.
- Highlighted FLOW in artwork is a **concept**, not proof its algorithm works.
- Physical bevels/reflections should not contaminate dynamic waveform mask or diminish contrast.
- Name VOX is provisional; carry a replaceable wordmark.
