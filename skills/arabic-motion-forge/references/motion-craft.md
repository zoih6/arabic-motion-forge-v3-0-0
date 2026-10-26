# Motion craft — the design contract

Design like a senior designer in a keyframe tool, where the code is your timeline.

## 1. Decompose before animating

For each shot, list the independently meaningful things: headline words, underline, ornament,
photograph, paper mount, shadow, chart bar, marker, number, background texture. Anything with a
different narrative function gets its own layer, its own channels and its own timing.

- Never animate a flattened group whose members need separate behaviour.
- Never bake a raster (photo + mount + shadow) that must move at different rates.
- Parenting is for intentional relations only; children keep channel independence.
- A composite that moves as one poster is the most common failure of AI motion.

## 2. Three-stage timing (every element, every time)

- **Before** — a defined off-state at a known time. Nothing is accidentally visible.
- **During** — explicit keys or an analytic curve with a named easing. Multi-stage motion has
  multi-stage keys (anticipation → travel → overshoot → settle), not one eased tween.
- **After** — settle, hold, or a motivated exit. An element that is "just there" afterwards was
  never designed.

Stagger start times. Nothing starts and ends with everything else. Total motion of a group should
feel like a phrase: a leader, followers, and one element that arrives late on purpose.

## 3. Channel economy

Use only the channels that serve meaning: position, scale, rotation, opacity, mask/clip, path
trim, depth (z), colour. A layer with five animated channels and no reason is noise; a layer
with one perfect channel can carry a film.

- Opacity-only reveals are the weakest option; prefer mattes, trims, clips, or structural builds.
- Overshoot only when the object has physical character (mass, elasticity, impact).
- No decorative wobble, no free-floating float without a metaphor, no constant rotation.
- Secondary motion derives from depth or physics ratios — never from an invented independent
  speed.

## 4. Reveals are engineered

- Arabic and long titles build via animated mattes, clipping regions, path trims or per-word
  selectors — not a global fade on split glyphs.
- A trimmed stroke's head marker is computed from the same arc-length parameter that drew it.
- A matte edge has a shape decision: hard rectangle, torn paper, ink bleed, brush. Pick one and
  keep it for the whole film.
- Text revealed in relation to something else (a rule drawing under a word, a highlighter behind
  a clause) reads faster than text that appears alone.

## 5. Camera is choreography, not garnish

Default is **static framing with layers moving**. Camera earns its move:

| Move | When it is right | Rules |
|---|---|---|
| static | busy content, data reading, most lower thirds | layers own all motion |
| slow push | a claim arriving, entering a diagram | one push per shot; settle before text resolves |
| lateral drift | surveying a map/timeline | direction matches reading order (right→left for Arabic) |
| focus travel | stacked evidence planes | blur distance ∝ |z − focus| |
| whip | linking two scenes, once | ≥4 temporal samples at 180° shutter; pre-position incoming content |
| reveal pull | ending wide after a detail | only when content outside the frame matters next |

Depth is a **number per layer**, and parallax falls out of projection:
`k = focal / (focal − z − dolly)`. Use focal ≈ 1400–2000 px at 1080 width for filmic falloff,
render plates ≥15 % larger than frame (overscan), roll ≤0.6° unless diegetic.

**If removing the camera move changes nothing, remove the camera move.**

True perspective is a capability, not a default: escalate to real 3D only when rotation or
perspective *is* the idea — planar quads under a perspective camera, Arabic shaped before it is
warped, no ray tracer, no Cinema4D claim.

## 6. Transitions carry a relation

A transition is an argument about how two shots are connected:

- shared shape (a circle in shot A becomes the product in shot B)
- match cut on meaning (same position, different object)
- occluder handoff (an object passes the lens and becomes something else)
- morph with real vertex correspondence
- type bridge (a word from shot A becomes the layout of shot B)

A preset wipe between unrelated shots is not design. Default to a hard cut inside a beat; use a
designed transition only at a beat boundary that needs the relation.

## 7. Motion blur and stutter are chosen, not defaulted

- Blur = temporal sampling: average N sub-frame samples inside a shutter window (e.g. 4–8 samples
  at 180°). Document the sample count; a static Gaussian blur is not motion blur.
- Stutter/posterize-time (8–12 fps) is for tactile, stop-motion, cut-paper, mechanical texture —
  applied to element transforms while the camera stays smooth. Say which and why.
- Never blur a text reveal to hide a shaping problem.

## 8. Continuity rules (checkable)

- **Identity** — an element persisting across a cut keeps one id and its colour/geometry lineage.
- **Light** — one key-light azimuth per scene, recorded in `style.json`; shadows and gradients
  derive from it.
- **Scale** — axis units persist through camera moves; type steps ≥1.5× and constant in a scene.
- **Colour claim link** — each semantic claim binds to one accent, reused exactly (no drift).
- **Time** — clocks/timelines advance monotonically; loops are intentional and flagged.
- **Phase** — accents land within ±1 frame of the onset they claim.
- **Speed** — a secondary plane moves at the ratio its depth implies, not an invented speed.

## 9. Anti-slideshow audit (objective, run on the draft render)

Sampled per 10 s window:

| Metric | Target | Meaning |
|---|---|---|
| longest still stretch | ≤ 3 s | sustained stillness is not "minimal design" |
| flat single-motion ratio | ≤ 55 % | share of time where only one thing moves |
| layered ratio | ≥ 45 % | share of time where ≥2 independent layers move |
| motion bursts per 10 s | ≥ 1.5 | rhythm: something must *happen* |

Failing this with a technically valid file means **re-choreograph**, not re-encode. Two specific
checks beat any score: (a) pick 3 random mid-shot frames and name what is moving in each;
(b) watch the draft muted and describe the film in one sentence.

## 10. Quality bar in one line

If a frame were printed as a poster it would hold up, and if the poster moved it would say
something the still could not.
