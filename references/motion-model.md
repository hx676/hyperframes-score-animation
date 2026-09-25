# Motion Model

## Event and cue model

Each staff has an ordered event stream. An event contains one or more mapped notes that start together. Store the event time separately from each note's duration and tie data.

Note On triggers:

- one landing cue;
- one note ring at the real notehead;
- one entry in the visual event stream.

Tie and sustain are not new Note On events. If the desired style requires an extended note to create another jump, derive continuation cues from the tie endpoint or release structure. They must be explicit, deterministic, and auditable, and must never duplicate the original onset ring by accident.

## Stable multi-ball slots

Use a fixed slot count per staff equal to the maximum simultaneous visual cardinality. For each consecutive event:

1. compute source landing/continuation points;
2. compute destination note points;
3. pair source and destination points with minimum-cost matching;
4. retain slot IDs for paired paths;
5. allocate unused slots to new destination notes;
6. fade unused source balls out and new destination balls in across the same interval.

This gives smooth chord splitting and merging. When a chord is followed by a single note, the unused balls must visibly converge or fade during the transition; they must not remain as floating balls after the target event.

Repeated notes at one coordinate still need independent cues. Do not use coordinate equality as a reason to suppress an event.

## Continuous jump path

For every adjacent cue pair, use the whole interval:

```text
progress = clamp((time - startTime) / (endTime - startTime), 0, 1)
position = lerp(start, end, progress)
position.z += sin(progress * PI) * jumpHeight
```

Adjust jump height from distance and interval, with a lower ceiling for very dense passages. Add continuation points only when each segment has enough time; otherwise the ball should take a direct path rather than perform an impossible catch-up.

Do not:

- hold at the old note and move only during the final 20 percent;
- reset a ball to the first note when a later event becomes active;
- replace a long continuous path with a frame-to-frame teleport;
- let an unmatched slot inherit an unrelated old position;
- use a physics engine whose result depends on wall-clock time.

For a left-to-right score, validate the reading-axis delta. A large negative delta is a diagnostic failure unless the engraving explicitly turns back.

## Camera

Treat the score as one continuous world strip or a connected surface. Do not implement page/system changes as camera jumps. Compute a monotonic read path from event centers and continuation points, add controlled look-ahead, then apply deterministic damping to a precomputed camera target.

The camera target should keep the active upper and lower regions plus the next readable score area visible. It may lag and anticipate, but it should not be directly assigned to a ball position on every frame.

Ensure the physical paper stage extends beyond the score and camera frustum. Missing stage coverage creates black voids during the long move.

