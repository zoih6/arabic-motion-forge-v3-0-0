# Arabic typography — mandatory before any Arabic text moves

Violations here have ruined real renders. Read this before you animate a single Arabic word.

## 1. Shape first, move second

Arabic letters are contextual: the glyph for a letter depends on its neighbours. The unit of
animation is a **shaped word** or a **shaped line** — never an isolated character.

```
✅ draw the whole word with the shaping engine, clip it with a matte, animate the matte
✅ per-word offsets inside one shaped line
❌ split the string into characters and transform each glyph
❌ letter-spacing applied mid-word (breaks joins visually)
```

If your tool offers a "characters" text animator, treat it as Latin-only. For Arabic, use
`words` / `lines` / `range by word` selectors.

## 2. Engine and font readiness

- Canvas 2D: `ctx.direction = 'rtl'`, `ctx.textAlign` set per layout, and let the platform's
  shaper (HarfBuzz via the browser) do the work. Do not implement shaping yourself.
- DOM: rely on the browser's shaping + `dir="rtl"` + `unicode-bidi`.
- **Wait for fonts before frame one**: `await document.fonts.load('700 64px "<Family>"')` then
  `await document.fonts.ready` (or `document.fonts.check`). A render started before fonts resolve
  silently falls back and produces a different film on every run.
- Ship the font file with the project. Do not trust system fonts (names differ across OSes).
- This skill bundles IBM Plex Sans Arabic (Regular + SemiBold, OFL) in
  `assets/fonts/` — copy it into the project's `assets/` and `@font-face` it.
- For other faces, download from Google Fonts / the foundry's OFL release, record family, version,
  licence and file hash in `assets/manifest.json`. No licence → no font.

## 3. Metrics are measured, not assumed

- Width: `measureText()` per line; never estimate from character count.
- Vertical: `actualBoundingBoxAscent` / `actualBoundingBoxDescent` — diacritics and descenders
  extend beyond the nominal size.
- Line box ≥ 1.35 × font size when tashkeel is present; ≥ 1.2 × without.
- Test with the **longest real string** including marks (ـٍ ـُ ـِ ـّ ـْ) and the tallest stack.
- Baseline rhythm: keep a consistent baseline grid across shots; type jumping 2 px between shots
  is visible even if nobody can name it.

## 4. Bidi hygiene

- Latin words, digits and units inside an RTL line are isolated runs:
  `\u2066 … \u2069` (LRI/PDI) or CSS `unicode-bidi: isolate`.
- Check punctuation at line ends, parentheses mirroring, and the placement of `%`, `×`, `+`, `:`.
- Declare ONE numeral system per film (`style.json.numeral_system`: `arabic-indic` ٠١٢٣ or
  `western` 0123) and use it everywhere, including charts and timecodes.
- Test at 1080 width **and** at 25 % thumbnail: bidi errors that look fine at full size often
  collapse when scaled.

## 5. Lam-alef, finals, and joins

- Verify the لا ligature and the final forms ـه / ـة at full scale in a still frame.
- Check that no letter pair lost its join after a transform (scale is safe; per-glyph rotation and
  splitting are not).
- Descenders (ج ح خ ع غ م) colliding on tight leading is the most common "professional-looking but
  broken" defect.
- Diacritics must never be clipped by a matte edge; extend the mask or clamp the text box.

## 6. Reveal techniques for Arabic (honest naming)

| Intent | Do this | Name it |
|---|---|---|
| text appears progressively | shaped-word matte band sweeping in reading order | "masked word reveal" |
| line builds left→right visually | matte sweep anchored to the RTL start edge | "mask reveal (RTL)" |
| typewriter feel | per-word matte reveal at fast cadence, cursor on the leading edge | "typewriter-style word reveal" — **not** typewriter |
| handwriting | requires authored stroke trajectories per letterform (a real font's outline is not a pen path) | "write-on (authored strokes)" |
| tracking in | animate tracking of the **word** (≥0.02 em range) or per-word spacing | "word tracking reveal" |
| scramble/glitch text | ASCII only, with explicit permission to alter copy | "ASCII scramble" |

Never claim After Effects' Write-On or a native "typewriter" effect: those imply glyph-level
generation that shaped Arabic cannot do.

## 7. Copy fidelity law

Approved wording is final. Never re-word, re-vowel, drop an alif, change ال/ة, or swap a synonym to
make a box fit. Instead: resize the layout, change the line breaks, reduce the size ladder, or ask
the user for an edit — with the read-time math that justifies it.

## 8. Typographic system (design, not default)

- One display + one body + at most one mono/numeric. Size ladder ≥ 1.5× between levels.
- Emphasis by weight contrast (900 vs 300) before colour or glow; never fake bold with a stroke.
- Hero display ≥ 96 px at 1080 width; small print uses a naskh-leaning body face for tashkeel.
- Arabic letter-spacing only ≥ 0.02 em and only for display tracking; zero on body text.
- Latin/numeric counters need their own face decision — a Latin default inside Arabic display type
  looks unconsidered.

## 9. QA — screenshots that prove it

1. Entrance mid-frame (around 40 % of the reveal): no half-shaped or disconnected glyph.
2. Full-size still of the final state: joins, ligatures, finals, marks clear.
3. 25 % thumbnail: hierarchy still readable, no merged letters.
4. Longest string test: the real worst-case line, not "أهلاً وسهلاً".
5. Bidi test: a line with a number, a Latin brand and a percent sign.
6. For claimed techniques: one frame that shows the mechanism (mask edge / marker at its correct
   position), not just the pretty end state.

## 10. Anti-catalogue

- Arabic text on a curved or 3D-warped path: shape first, then warp the rasterised line as a whole
  plane. Per-letter warping is illegal.
- Animated outlines around Arabic: outline is a separate path derived from the shaped line, not a
  per-glyph stroke.
- Text as texture (repeated lines in the background) must still use shaped lines; a Latin filler
  string in an Arabic poster is a tell.
