# VOX — Open-source, dependency and sample-asset policy

**Decision:** Original code uses **AGPL-3.0-only**, consistent with Shortwav Labs AFTERIMAGE's intended JUCE open-source development approach. The full GNU AGPLv3 text is in the repository root [LICENSE](../LICENSE).

JUCE 9's open-source AGPLv3 terms, pinned exact release, copyright and bundled dependency notices **must be verified again** before importing framework code. AGPL's terms apply to distributed source/binaries; the copyright in third-party sound recordings is unrelated and requires separate rights.

## Third-party components / fonts / rendering

Keep a versioned inventory of libraries, bundled codecs, WAV decoder, algorithms, source, license and required notice placement. Blender-native procedural material assets are preferred. Don't commit a proprietary font, photo, texture, voice sample or trademarked competitor UI. Blender assets and exported sprite images need documented corresponding sources and rights.

## Samples and presets

- Never commit Freesound/Splice/vendor or user sample recordings without a specific redistribution license and credit obligations. A CC BY or NC file has limitations; don't assume “downloadable” means “commercially reusable.”
- The contest demo video and any bundled preset/demo audio require independently cleared rights. For P0 local offline testing, keep input/render WAVs git-ignored; write private metadata and URLs/author/license/hashes if known. Unknown rights means no publishing derived audio.
- Host projects may refer to an imported file; implement a clear privacy-preserving persistence and relink contract. The user's audio stays on their computer; no upload, telemetry or speech recognition by default.
- “VOX”, names, third-party logos and brand imagery are not cleared by the software license.

## Contest/release gates

Confirm https://www.theaudioprogrammer.com/events/plugin-jam-rules near submission: January 31, 2027 11:59 PM PT, **one entry per person/team**, public GitHub repository, OSI-approved source license, downloadable working plug-in (supported formats per rules), signed/notarized as applicable, demo video ≤3 minutes and all rights secured. AGPL-3.0-only is an OSI-approved license. Do not promise a release before build, host validation, signing and sources match.

Use exact tagged corresponding source, build instructions, reproducible dependencies, third-party notices and checksums for a release. No shipping binaries, notarization or installer work is part of this design PR.
