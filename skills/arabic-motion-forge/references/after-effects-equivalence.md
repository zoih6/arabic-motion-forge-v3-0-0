# After Effects equivalence — think like AE, claim only what you built

You should design with an AE-level mental model: layers, comps, track mattes, shape layers,
trim paths, text animators, keyframes, graph editor, time remapping, cameras, trackers.
The environment you actually run in is code. Both are legitimate; blurring them is not.

## 1. Equivalence table

| AE concept | What you implement | Honesty label |
|---|---|---|
| Layer + parent | layer list with optional parent transform, channels kept independent | native equivalent |
| Alpha track matte | one layer drawn into an offscreen buffer, used as clip/alpha for another | native equivalent |
| Luma matte | luminance→alpha conversion with a declared Rec.709 weighting on sRGB values | equivalent — not identical to every AE colour setting |
| Shape layer + Trim Paths | path metrics by arc length, draw `[0, s(t)]`, head marker at the same parameter | native equivalent |
| Text animator (range by word) | per-word offsets from one shaped line | native equivalent for Arabic-safe units |
| Text animator (range by character) | Latin only | not supported for Arabic (by design) |
| Keyframes + graph editor | keyframe list + easing functions; value curve, derivative = speed | equivalent — sampling differs |
| Motion blur | temporal supersampling inside a shutter window (N samples) | equivalent, documented sample count |
| Posterize Time | `floor(t × rate) / rate` applied to selected transforms | native equivalent |
| 3D layer / camera | perspective projection with planar quads (or three.js) | approximate — no ray tracer, no C4D |
| Depth of field | per-plane static blur stepped at keys | approximate, declared in plan |
| Puppet / mesh warp | subdivide + affine per cell | approximate |
| Particular / particles | your own deterministic particle field, seeded | not equivalent — different tool |
| Expressions | plain functions of `t` in code | equivalent concept |
| Render queue / AME | headless Chromium + ffmpeg, checkpointed | native equivalent of the *result*, not the app |

Rule: a name from the left column is a **design vocabulary**, not evidence that the tool exists.
Every claim in a report uses the middle column's reality.

## 2. When the user works in After Effects

The deliverable is a **bridge**, not a claim of parity:

1. Deliver the MP4 master first — that is always correct.
2. Optionally generate an `ExtendScript (.jsx)` that builds comps and layers: comp size/fps/duration,
   per-layer in/out points, position/scale/rotation/opacity keyframes with eased interpolation that
   approximates the curves, and placeholders for footage and glyph sources.
3. Name it `<project>-ae-bridge.jsx` and label it "bridge script (approximate, requires relinking)".
   Never call it an AE project, and never say AE rendered it.
4. List what will not transfer: text animators, trim paths with custom head markers, luminance matte
   colour handling, 3D plane warps, procedural noise.

## 3. Design vocabulary parity (what to borrow from AE practice)

Borrow the discipline, not the menus:

- **Comp structure**: one comp per scene, one timeline per film; precomps only for reused elements.
- **Layer order + track mattes**: keep a strict, readable stack; name layers by function.
- **Two-key minimum**: nothing animates with a single key — define off-state and on-state.
- **Graph editor thinking**: shape the value curve; verify the resulting speed curve makes sense.
- **Trim paths**: if a line draws, its marker rides the same parameter.
- **Time remapping**: any speed change is a decision with a reason (impact, slow-motion reveal).
- **Solos and shy layers**: review the film with only one element visible to check each channel.

## 4. Reporting template

```
Delivered: <file> (1080×1920, 30 fps, 27.4 s, h264, -14.2 LUFS, TP -1.8 dBTP, sha256 …)
Built with: HTML/Canvas 2D + headless Chromium, ffmpeg 7.x; deterministic seek(t) verified
Equivalent to: masked word reveals, trim-path line draws, 2.5D parallax (documented)
Not equivalent / not available: AE-native text animators, depth of field, real 3D renderer
Unverified: audio mix by ear (agent cannot listen); no device-level playback test
Next: <exact next command or step>
```
