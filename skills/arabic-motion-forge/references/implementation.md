# Implementation — from timeline to a verified MP4

You write the code. This file gives the architecture, the commands and the license boundaries.

## 1. Choose the smallest engine that works

| Scene | Stack |
|---|---|
| typographic / editorial / 2.5D (most jobs) | HTML + Canvas 2D (or SVG) with `seek(t)`, rendered headlessly by Chromium, piped to ffmpeg |
| DOM-heavy layouts, CSS animations | HTML + DOM + Web Animations API, still rendered headlessly frame by frame |
| many layers with easing already modelled | Canvas 2D + a tiny easing module you write (~40 lines) |
| React-shaped projects, component reuse | Remotion (check license: free for individuals/small teams, paid for companies — verify current terms before shipping) |
| real 3D | three.js or a hand-written perspective compositor; do not over-engineer |
| interactive or GSAP-based | GSAP (free core, plugins differ in license — verify before shipping) |

Rules: no engine that cannot render frame-accurately from a time value; no library you cannot
license for the user's use; no dependency without a reason (a 3-line easing function beats a
150 KB animation library here).

## 2. The render entry must be a pure function of `t`

```js
// src/index.html — one global, no animation loop, no timers, no randomness
let READY = false;
const DURATION = 30, FPS = 30;

export async function init() {
  await document.fonts.load('700 96px "IBM Plex Sans Arabic"');
  await document.fonts.ready;
  await Promise.all(images.map(loadImage));       // all assets awaited
  READY = true;
}

export function seek(t) {                          // t in seconds, 0 <= t < DURATION
  ctx.setTransform(1,0,0,1,0,0);
  ctx.clearRect(0, 0, W, H);
  for (const shot of SHOTS) {
    if (t < shot.t0 || t >= shot.t1) continue;
    shot.draw(ctx, t - shot.t0);                   // each shot is a pure draw
  }
}

window.__forge = { init, seek, DURATION, FPS, get ready() { return READY; } };
```

Bans inside `seek`: `Math.random`, `Date.now`, `requestAnimationFrame`, `setTimeout`, mutable
module state that persists between calls, and anything that depends on how many frames were
rendered before. Caches keyed by layer id are acceptable only if they are re-computable.

Determinism smoke test in the page:

```js
const a = digest(t=3.2); const b = digest(t=7.7); const a2 = digest(t=3.2);
console.assert(a === a2, 'non-deterministic seek');
```

## 3. Render loop (headless Chromium → ffmpeg)

Node + Playwright/Puppeteer, streaming raw RGBA into ffmpeg's stdin (no PNG dumping):

```bash
node render.mjs --entry src/index.html --w 1080 --h 1920 --fps 30 --dur 30 \
  --out build/part-01.mp4 --from 0 --to 10
# inside render.mjs:
#   await page.evaluate(t => window.__forge.seek(t), t)
#   const buf = await page.screenshot({ type: 'png' })  // or canvas.toDataURL / raw readPixels
#   pipe to: ffmpeg -f rawvideo -pix_fmt rgba -s 1080x1920 -r 30 -i - \
#                 -c:v libx264 -preset slow -crf 18 -pix_fmt yuv420p out.mp4
```

Practical notes:
- JPEG/PNG screenshots are slow; for speed read the canvas pixels (`gl.readPixels` on WebGL, or a
  `toBlob` + stream) and pipe raw frames.
- Encode in **checkpoint parts** (10 s each): a crash costs one part, and unchanged parts are
  reused on re-edits.
- Draft tier for review: `crf 28`, half resolution, same fps — motion review does not need pixels.
- Bitrate/quality: `crf 18–20` for masters, plus a delivery copy capped to the platform's size budget
  (`bitrate_kbps ≈ MB × 8000 / seconds`). Always `-pix_fmt yuv420p` for compatibility, `-movflags +faststart`.

## 4. Audio mux (one pass, after video parts are concatenated)

```bash
ffmpeg -f concat -safe 0 -i parts.txt -i mix.wav \
  -map 0:v -map 1:a -c:v copy -c:a aac -b:a 192k -ar 48000 \
  -shortest -movflags +faststart final.mp4
ffmpeg -i final.mp4 -af ebur128=framelog=quiet -f null -   # measured loudness
```

## 5. Verify the encoded file (not the design)

```bash
ffprobe -v error -select_streams v:0 -count_frames \
  -show_entries stream=nb_read_frames,r_frame_rate,width,height,duration \
  -of default=nw=1 final.mp4
ffprobe -v error -select_streams a:0 -show_entries stream=codec_name,sample_rate,channels \
  -of default=nw=1 final.mp4
ffmpeg -v error -i final.mp4 -f null -                      # full decode; errors = failure
ffprobe -v error -show_entries packet=pts_time -of csv=p=0 final.mp4 | sort -n | \
  awk 'NR>1{d=$1-p; if (d<=0) {print "PTS ORDER FAIL at",$1; bad=1}} {p=$1} END{if(!bad) print "PTS OK"}'
sha256sum final.mp4
```

Expected: `nb_read_frames == round(duration * fps)`, `r_frame_rate` equal to the intended rate
(compare as fractions, `32/1 == 32.0`), constant PTS increments, a decode pass with no errors, and
an audio stream present.

## 6. Review renders

- Contact sheet: 8 stills max, ≤480 px each, one per beat, labelled with timecode — written to a
  file, inspected selectively.
- Spot-check frames inside transitions and at reveal mid-points (the two places defects hide).
- Keep every report in the project (`journal.md` entries + `deliverables/report.md`).

## 7. Performance and memory

- One renderer process at a time; background process + wait on a log pattern, never a foreground
  loop with tight polling.
- Free page state between parts if memory grows (`page.reload()` between parts is cheap insurance).
- Long films: render parts in parallel only if the machine has the cores and RAM; otherwise
  sequential is faster than swapping.
- Never paste base64 images into the chat; write files and report paths + hashes.

## 8. Licensing honesty (verify, never assume)

- **GSAP**: core is free; some plugins are Club-only — check before shipping.
- **Remotion**: source-available with company-size terms — check the current license.
- **Theatre.js**: check the notice/package metadata mismatch before bundling.
- **three.js, Motion One, anime.js, d3, flubber, harfbuzzjs, opentype.js, lottie-web, pixi.js,
  rough.js**: permissive (MIT/ISC/BSD) — still keep their notices in `deliverables/NOTICES.txt`.
- Fonts: OFL/Apache only for embedding; record the licence next to the file.

## 9. Accessibility and hygiene

- Contrast: text over imagery needs a matte band or ≥4.5:1 local contrast.
- Nothing critical inside the platform UI band or within 6 % of the frame edge.
- No flashing >3 times/second; no strobe effects on full-frame contrast.
- Keep the project reproducible: pin versions in a `package.json` and record them in the journal.
