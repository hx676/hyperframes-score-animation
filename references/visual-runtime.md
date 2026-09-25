# Three.js, WebGPU, and HyperFrames Runtime

## Seek-safe rendering

The scene must render from an explicit time:

```js
window.addEventListener("hf-seek", (event) => {
  const time = Number(event.detail?.time) || 0;
  const work = renderAt(time);
  event.detail?.waitUntil?.(work);
});
```

`renderAt(time)` must update ball states, rings, trails, lights, camera, and renderers without consulting wall-clock time. Wait for submitted GPU work when the renderer exposes a completion promise.

Expose the composition's build promise through the HyperFrames `buildReady` contract. Keep the pinned HyperFrames CLI and Three.js versions in `package.json`.

## Renderer strategy

Use Three.js `WebGPURenderer` when the target supports it, with a deterministic WebGL fallback for headless capture when necessary. Do not assume that a successful WebGPU initialization means the capture path is valid; test the actual CLI render.

For high-resolution output, do not leave the renderer pixel ratio at `1`:

```js
const pixelRatio = Math.max(1, Math.min(window.devicePixelRatio || 1, 4));
renderer.setPixelRatio(pixelRatio);
renderer.setSize(compositionWidth, compositionHeight, false);
```

## Ball and trail treatment

Use a physical ball mesh with a bright core and restrained emissive/glow layers. A sprite alone reads as a flat overlay. A small point light near each active ball can create contact color on the paper, but it must not wash the full page.

Build trails from deterministic historical samples of the same motion function. Instanced meshes or pooled particles are preferred. Keep trails short and tapered: the trail explains motion, it does not become a second subject.

## Score and lighting

Use real score textures or vector-derived textures. Keep paper warm and slightly rough, ink dark but readable, and the environment dark without leaving uncovered black areas. Use controlled tone mapping, subtle vignette, and restrained bloom. A darker grade must not reduce the notehead contrast needed for mapping comprehension.

