---
name: hyperframes-score-animation
description: "Build or audit HyperFrames 3D score animations driven by real MusicXML/MXL, MIDI, and audio, with verified note mapping, deterministic multi-ball motion, continuous camera movement, and reproducible video rendering."
---

# HyperFrames Score Animation

Use this skill when the deliverable is a HyperFrames composition in which animated objects perform against a real musical score. It is for independent HyperFrames builds using HTML plus Three.js/WebGPU/WebGL or TypeGPU; do not port or reuse a Remotion implementation unless the user explicitly asks for a port.

Read the owning HyperFrames skills before authoring:

- `/hyperframes-core` for the composition contract and deterministic seeking.
- `/hyperframes-animation` for seek-safe motion and the selected Three.js or TypeGPU adapter.
- `/hyperframes-cli` for check, snapshot, render, and publish.
- `/hyperframes-keyframes` when the camera or score is treated as a keyframed 3D move.

## Non-Negotiable Invariants

1. Inventory every supplied asset before building. Identify the reference video, score source, MIDI, audio, and any fonts or textures.
2. Never guess duration from a brief. Use the source audio duration when audio is supplied; otherwise use the verified MIDI end plus an intentional tail.
3. Never place notes with `noteIndex * constant` or a time-to-screen proportional shortcut. Render the actual MusicXML/MXL with Verovio or OpenSheetMusicDisplay and extract notehead geometry from the rendered SVG.
4. Every audible MIDI onset that is mapped to the score must have a visual response. Chords and voices may use several balls, but ball count is an explicit visual policy, not an accidental side effect.
5. Tie, sustain, and release data must be modeled separately from Note On. A tie must not create a false new onset; if the visual design makes a continuation jump, create a deterministic continuation cue and label it as such.
6. Use the complete interval between consecutive cues for motion. A ball must not wait and then catch up in the last fraction of the interval.
7. Ball identities must use stable slots across chord split and merge transitions. Do not recreate, sort, or reassign balls every frame.
8. A continuous score-reading shot must not move backward on the reading axis unless the engraved score itself requires it. Reject or diagnose assignments that cause a backward jump or discontinuity.
9. All frame output must be a pure function of frame/time and precomputed data. Do not use `Date.now()`, `performance.now()`, `requestAnimationFrame`, random values, or live physics.
10. Do not report a successful render from a version check alone. Verify mapping, motion continuity, seek behavior, video streams, audio streams, and representative frames.

## Standard Production Loop

1. Inspect the project and assets; preserve existing dependencies and the pinned HyperFrames CLI.
2. Parse MXL/MusicXML into note, staff, voice, measure, pitch, duration, tie, and system data.
3. Render the score as vector or a derived high-resolution texture. Extract notehead coordinates and assert that the score order and rendered order agree.
4. Parse MIDI into Note On/Off, pitch, velocity, track, channel, tick, and second-based timing.
5. Determine audio alignment from onset anchors across the beginning, middle, and end. Apply a fixed offset only when drift is absent; otherwise conform or replace the audio with a MIDI-synchronous piano track.
6. Build a frozen score-data artifact containing duration, tempo map, mapped notes, event groups, tie continuation cues, page/system geometry, and camera read points.
7. Build deterministic ball paths, stable slot assignments, ring cues, trails, and a monotonic camera focus path.
8. Implement HyperFrames seeking. A Three.js render must listen for `hf-seek`, render the requested time, and pass the GPU completion promise to `event.detail.waitUntil()` when available.
9. Run the short preview and the full motion audit before final export. Fix the data or motion model, not just the camera, when a ball skips or lands on the wrong note.
10. Render a delivery MP4, then render 4K only after the standard result is verified. Verify with FFprobe and sample frames from quiet, dense, chord, tie, and high-speed sections.

## References

- Read [references/pipeline.md](references/pipeline.md) for the end-to-end project layout and artifact contract.
- Read [references/mapping-and-timing.md](references/mapping-and-timing.md) for score-to-MIDI mapping and audio synchronization.
- Read [references/motion-model.md](references/motion-model.md) for multi-ball, tie, no-teleport, and camera rules.
- Read [references/visual-runtime.md](references/visual-runtime.md) for the Three.js/WebGPU/WebGL implementation pattern.
- Read [references/validation-checklist.md](references/validation-checklist.md) before calling a render complete.
- Read [references/4k-rendering.md](references/4k-rendering.md) when exporting high resolution.

## Completion Standard

The final response should name the score, MIDI, and audio used; state whether an offset or tempo correction was applied; report the mapping and audit results; give the exact output paths; and include the commands required to rebuild the data and render the video.
