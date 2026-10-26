# Space mechanics — depth, camera, 3D, scene transitions

Depth is a design decision with numbers, not a vibe.

## 1. 2.5D multi-plane (the default)

Give every layer a `z` (px). Project with a pinhole camera:

```
k(z)  = focal / (focal - z - dolly)      // scale factor for that plane
p(z)  = k(z) / k(z_ref)                  // parallax ratio
x' = (x - cam.x) * p + cam.x
```
- `focal` 1400–2000 px at 1080 width; plates rendered ≥15 % oversized (overscan).
- Keep every plane in front of the camera: guard `k > 0` (a plane crossing the camera explodes).
- Depth plan: background 0, mid-ground 120–200, subject 300–450, foreground overlay 600+.
- Secondary planes inherit `p(z)` — never hand-tune their speeds.
- Blur for depth is a per-plane constant stepped at keyframes (no real DOF): document it.

## 2. Focus travel and rack focus

`blur(z) = clamp(|z − focus| * rate, 0, maxBlur)`, `focus` animated with an ease-out. Keep the
subject plane at blur 0 while the viewer must read it. Rack focus is a strong attention tool: use
it once per scene.

## 3. When to escalate to true 3D

Escalate only if rotation, thickness or perspective **is** the idea (a box turning, a card flying
in at an angle, a camera orbiting an object). Rules:

- Real perspective projection with planar quads drawn as two affine triangles (exact texture
  mapping) is the honest ceiling for 2D-canvas renderers.
- Convex quads only; subdivide large walls instead of cheating the near plane.
- Stable painter's sort (sort by depth once per frame from `t`), no frame-mutable z state.
- One Lambert-ish shading model from a single light azimuth recorded in `style.json`.
- Cost honesty: 3D scenes render ~1.5–2× slower; budget accordingly.
- **Arabic on a 3D plane**: shape the words first, rasterise/vectorise the line, then warp the
  plane. Never project per letter.
- Never claim a ray tracer, Cinema4D or native After Effects 3D.

## 4. Camera move vocabulary

`static` · `slow push` · `lateral drift` · `focus travel` · `whip` · `reveal pull` · `orbit`
(3D only) · `handheld` (small seeded noise ≤0.4 % + ≤0.3° roll).

Rules: one move per shot; a move must be motivated by the focal element; settle before text
resolves; no arbitrary zoom on every shot; handheld needs a reason (documentary energy, not
default "cinematic").

## 5. Scene transitions

A transition is a **relation**. Choose by the relation, then build the geometry:

| Relation | Build |
|---|---|
| same object, different context | match cut: identical screen position + scale at the cut; sample 2 frames either side |
| continuity of direction | cut on motion: outgoing velocity continues, angle delta < 25° |
| an object takes over the frame | occluder handoff: a foreground layer grows past frame, incoming shot pre-positioned |
| two shapes are the same shape | shape correspondence: real vertex correspondence, morphed over 6–12 frames |
| a spatial link (same building, other room) | camera-through-window: dolly into an opening, incoming shot revealed by the aperture |
| language link | type bridge: a word from A becomes the layout position of B |

Whip pan: ≥4 temporal samples at 180° shutter, pre-position incoming content, no more than once
per film. Zoom transition: the entering element must exist in both shots (a real scale continuity),
not a scale burst between unrelated frames.

## 6. Occluders and wipes with objects

- The occluder is a real layer with its own depth, shadow and easing.
- Mask, occluder and next shot share one geometry — derive all three from the same path/rect.
- Feather ≤4 px; the edge must be visible enough to read as a physical object.

## 7. Staging and composition for vertical frames

- 9:16: keep the subject inside the middle 80 % vertically; the bottom 12–18 % belongs to platform
  UI; safe margins ≥ 6 % on each side.
- Eyes and headline centres sit in the upper third; CTAs and logos in the lower third but above
  the UI band.
- Depth reads poorly in narrow frames — prefer one foreground overlay element rather than four
  planes.
- Text over a moving background: reduce background contrast/saturation, or band the text on a
  matte. Never rely on a drop shadow to fix legibility.

## 8. Verification for space

- Sample mid-motion frames: horizon/line continuity, no plane explosion, no z-fighting flicker.
- Determinism: identical frames at identical `t` in different call order.
- Overscan check: no empty edge appears during the largest camera move.
- Parallax sanity: nearest plane moves more than the far plane, always.
