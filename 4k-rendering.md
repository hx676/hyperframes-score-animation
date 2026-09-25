# 4K Rendering

Treat 4K as a second verified export, not as the first debugging target.

## Before exporting

1. Pass the standard resolution build, runtime check, motion audit, and representative frame review.
2. Confirm the renderer uses the browser/device pixel ratio up to a controlled cap.
3. Keep the composition dimensions stable; let the capture preset determine the output resolution.
4. Use a pinned CLI command with explicit fps, resolution, quality, CRF, workers, and output path.

Example:

```powershell
npx hyperframes@0.8.76 render `
  --quality delivery `
  --fps 60 `
  --resolution landscape-4k `
  --crf 10 `
  --workers 2 `
  --output out/final-4k-60fps.mp4
```

Use the project's pinned version rather than silently changing the renderer. Probe upgrades before applying them and re-run `check` after a version change.

## Verify the file

Use FFprobe to confirm:

- 3840x2160 or the requested 4K dimensions;
- expected frame rate;
- complete duration;
- H.264 or the requested video codec;
- AAC or the requested audio codec;
- expected sample rate and channel count.

Also check for black frames and inspect frames from the beginning, a dense section, a chord section, and the ending. 4K capture can take much longer and produce a much larger file; keep the delivery render as a practical review artifact.

