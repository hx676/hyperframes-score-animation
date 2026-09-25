# Validation Checklist

Run checks in this order. Do not skip the data audit because the final MP4 can look plausible while mapping the wrong note.

## Data and build

- `npm run build` completes.
- score and MIDI files are the intended pair.
- audio duration is known.
- mapped MIDI count equals the expected audible onset count.
- no unmapped notes or missing notehead geometry.
- staff, voice, pitch, chord, repeated-note, measure, and tie diagnostics pass.
- generated data is deterministic across two builds.

## Motion audit

Sample the entire duration at the delivery frame rate and at a lower diagnostic rate. Report:

- maximum single-frame movement;
- samples exceeding the chosen no-teleport threshold;
- backward reading-axis steps;
- boundary position gaps;
- alpha/scale gaps at event boundaries;
- incorrect landing count and worst landing error;
- maximum ball cardinality versus event chord cardinality;
- floating balls after chord-to-single transitions.

Use higher-risk samples around dense passages, ties, repeated notes, chord splits/merges, and any outlier found by the audit. A short preview is not a full-track validation.

## Runtime and video

- `npx hyperframes check` passes, or any known canvas diagnostic is documented.
- seek to the first frame, a quiet frame, a chord frame, a tie frame, a dense passage, and the final tail.
- confirm no black or uncovered stage region appears.
- confirm the camera remains continuous and readable.
- use FFprobe to verify resolution, frame rate, codec, duration, audio sample rate, and channels.
- inspect representative images from the encoded MP4, not only the live preview.

HyperFrames' `sweep_static` check can report a false positive for a canvas-only Three.js/WebGPU scene when its pixel sampler does not observe GPU changes. Treat it as unresolved until real seek snapshots and encoded video prove motion; do not add meaningless DOM animation solely to satisfy that diagnostic.

## Completion report

Report the exact:

- source score, MIDI, and audio;
- measured offset and tempo decision;
- mapping method;
- video duration and frame rate;
- audit counts and worst-case values;
- preview and final MP4 paths;
- synchronized audio path;
- rebuild and render commands.

