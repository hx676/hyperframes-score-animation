# Score Mapping and Audio Timing

## MusicXML/MXL parsing

For MXL:

1. Read `META-INF/container.xml`.
2. Resolve the declared root MusicXML path.
3. Use an XML parser, not regular expressions, for measures, attributes, notes, backups, forwards, voices, staffs, ties, and pitches.
4. Preserve source IDs and create stable internal IDs.

Track measure-local cursor movement correctly. Chord notes share the previous onset; `backup` and `forward` change the cursor. A parser that only increments linearly will misplace voices and chords.

## Rendered geometry

Render the actual MusicXML with Verovio or OpenSheetMusicDisplay. In Verovio SVG:

- locate note groups;
- locate the notehead element or its `<use>` child;
- accumulate ancestor transforms;
- convert the rendered SVG coordinates into the page coordinate system;
- keep page, system, measure, staff, and source/render IDs.

The visual target is the notehead, not the bounding box of the whole note group. If the renderer emits MEI IDs, assert that the source note and rendered note agree in pitch, staff, and voice before accepting the coordinate.

## MIDI mapping

MIDI controls time. MusicXML controls visual structure. Build the relation:

```text
MIDI Note On -> score note -> rendered notehead position
```

Prefer onset-group matching:

- group simultaneous MIDI notes with a small, data-driven tolerance;
- group score onsets by measure/voice/staff and pitch multiset;
- match pitch multisets, staff/voice, duration, and order;
- validate repeated pitches and repeated chords explicitly.

A simple same-pitch queue is acceptable only when the score order, staff, voice, onset order, and count have been asserted. If repeated notes or voices make the match ambiguous, stop and diagnose rather than silently selecting the nearest note.

Validate at least:

- MIDI note count versus mapped count;
- unmapped note count;
- missing notehead geometry;
- staff and voice agreement;
- event/chord pitch multisets;
- repeated same-pitch note order;
- the first, middle, and final mapped regions.

## Tempo and audio

Do not assume the MusicXML tempo marking is the playback tempo. MIDI tempo events and the actual audio may disagree with the score's printed BPM.

Use MIDI event seconds for animation. Compare the audio against MIDI using onset anchors near the beginning, middle, and end:

- constant error: apply one measured offset;
- increasing/decreasing error: there is tempo drift; do not hide it with a constant offset;
- uncertain or poor onset detection: inspect the waveform and choose a safer audio source.

Keep the MIDI event times unchanged when the visual mapping is already correct. Add a single timeline offset for an intro only after applying the measured source-to-MIDI alignment consistently to the audio and animation timeline.

If the supplied performance drifts against the MIDI, generate or select a MIDI-synchronous piano track instead of stretching the animation to the recording. Synchronization has priority over preserving a mismatched performance.

Record:

```text
source audio duration
measured source-to-MIDI offset
timeline intro offset
tempo scale
whether the audio was replaced or conformed
```

