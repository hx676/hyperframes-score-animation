# Pipeline and Artifact Contract

This workflow is intended for a reusable HyperFrames project, not a one-off timeline hack.

## Suggested project shape

```text
assets/
  score.mxl
  score.musicxml
  performance.wav
src/
  scene.js
  ball-motion.js
  data-loader.js
scripts/
  build-score-data.mjs
  audit-motion.mjs
  audio-sync-config.mjs
assets/score-data.js
out/
  preview.mp4
  final.mp4
README.md
package.json
```

Reuse the existing project structure when one exists. Do not initialize a second project beside a working project merely to change the renderer.

## Required frozen data

The preprocessing step should produce one deterministic data artifact with at least:

```text
duration
fps
audioSync
score.pages or scoreSystems
notes[]
events[]
tieEdges[]
cameraReadPoints[]
stats
```

Each mapped note should retain:

```text
id, renderId, midiNote, pitch, staff, voice, measureIndex,
startTime, duration, x, y, pageIndex, systemIndex
```

Each event should retain its onset time, staff, note list, visual center, and any continuation cues. Do not parse MusicXML again during frame rendering.

## Build order

1. Discover and identify assets.
2. Parse and validate score structure.
3. Parse MIDI and compute real seconds.
4. Render the score and extract geometry.
5. Align MIDI notes to score notes.
6. Measure audio alignment.
7. Emit the frozen data artifact.
8. Run mapping diagnostics before building visual effects.
9. Build the scene and motion model.
10. Run a short render, then a full audit, then delivery and 4K renders.

## Failure policy

Fail closed when the score cannot be rendered, the note count differs unexpectedly, notehead geometry is missing, staff/voice order diverges, or an onset cannot be mapped with a defensible match. A visible but wrong note is worse than a build error.

Allow a manual mapping file only when the source files genuinely differ. Record the reason, the matching rule, and the resulting statistics in the build output.

