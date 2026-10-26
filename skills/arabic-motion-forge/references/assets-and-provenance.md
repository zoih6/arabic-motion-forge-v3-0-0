# Assets & provenance — every pixel has a source

Assets are arguments, not decoration. Each one needs a function, a source and a licence.

## 1. Provenance record (mandatory)

`assets/manifest.json`, one entry per file:

```json
{ "file": "assets/photo-01.jpg",
  "kind": "genuine | generated | illustrative | archival-licensed",
  "source": "https://…", "author": "…", "license": "CC BY 4.0",
  "retrieved": "2026-10-25", "sha256": "…",
  "mods": "cropped 4:5, background removed, colour matched to palette",
  "role": "the shop facade the narration names" }
```

- `genuine` = real photograph/recording of the thing. `generated` = AI/hand-made imagery.
  `illustrative` = diagram or drawn reconstruction. `archival-licensed` = historical material with
  a licence.
- Never present an imagined historical figure or artefact as archival documentation. Label the
  reconstruction in the credits and, when it matters, on screen.
- No licence, no asset. If a licence is unclear, do not ship it.

## 2. Cut-outs and mattes

- Inspect alpha edges at 400 % over **two** backgrounds (dark and light). Halos, fringing or a
  leftover sky rectangle mean re-matte, not "fix in grade".
- Edge contract: ~1 px refinement, feather ≤ 0.5–1 px; a 3 px feather on flat art is the classic
  cheap tell.
- Preserve soft edges on hair/glass/smoke; treat them as separate matte work.

## 3. Layers, shadows, mounts

- A photo, its paper mount and its shadow are three layers whenever they move differently.
- Shadows derive from the silhouette (offset + blur + opacity from the same elevation value), never
  a duplicated darkened rectangle.
- One key-light azimuth per scene, recorded in `style.json`; all shadows and gradients follow it.
- Mounts/frames are their own geometry so they can enter, rotate or cast independently.

## 4. Charts and diagrams

- Geometry from the data table in code — never hand-placed bars.
- Record the data source, the axis units, and the transformation (index, per-capita, log).
- Constant visual encoding within a scene (one colour = one category).
- Entrance: stagger by data, not alphabetically; the last bar to arrive should be the point.
- Never invent or "round for beauty" a number that appears on screen. Mark illustrative values as
  illustrative in the spec, and prefer no number over a fake one.

## 5. Generated imagery

- Write the prompt into the manifest, label `generated`, and keep the seed/version if the tool
  reports it.
- Generated images may not stand in for documentary evidence; they are conceptual.
- Consistency: regenerate with the same prompt family + palette, or unify in code (grade, grain,
  edge treatment) so a set reads as one world.

## 6. Vector and procedural art

- Build motifs as parametric functions of `t` when they must animate (a rule that draws, a pattern
  that reacts).
- Static SVG for hold art; keep paths as data so trims and morphs are exact.
- Morphs need real vertex correspondence (same count, same winding order); if the shapes cannot
  correspond, choose a different transition instead of a crossfade.

## 7. Memory and storage discipline

- Process images in batches; free between batches. Never hold a stack of hundreds of 1080p frames.
- Images ≤ 4096 px unless there is a stated reason; video sources streamed through ffmpeg filters,
  not decoded fully into memory.
- Check free space before writing anything large; put masters outside the deliverable folder and
  hand the user only capped copies (light ≤25 MiB, high ≤50 MiB, plus a poster still).
