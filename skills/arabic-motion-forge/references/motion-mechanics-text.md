# Text mechanics — reveals, kinetic type, line work

Naming authority: a "motion name" is a **behaviour**, not a tool. Many labels share one
mechanism. Implement the mechanism; label the result honestly.

| Label(s) | Mechanism |
|---|---|
| Fade In, Slide In, Scale Up, Spin In, Push, Pull | transform track (position/scale/rotation) |
| Pop In, Scale Bounce, Spring In, Overshoot & Settle | anticipation + spring settle |
| Reveal In, Wipe In, Mask Reveal, Clipping Reveal, Split Reveal | alpha matte / clipping region |
| Word-by-Word, Line-by-Line, Cascade, Word Stagger, Stagger | range delay on units |
| Wave, Ripple, Baseline Wave, Character Lift | per-unit offset curve over time |
| Underline Draw, Line Draw, Arrow Draw, Callout | path trim + head marker |
| Typewriter (Arabic) | shaped-word matte reveal — not a glyph typewriter |
| Typewriter (Latin) | prefix reveal of characters, honest only for LTR monospaced/simple text |
| Scramble | ASCII-only, requires explicit permission to change copy |
| Write On | real authored stroke trajectories only |

## 1. Unit selection (the first decision)

The animation unit defines what the viewer perceives. In order of preference:

1. **line** — safest, most readable, best for long Arabic clauses.
2. **word** — default for Arabic kinetic type; keeps shaping intact.
3. **glyph cluster** (a shaped run) — only with a shaping engine; never by `split('')`.
4. **character** — Latin/CJK-permissible only.

Set the unit once per film in `style.json` (`text_unit`) and keep it consistent; switching units
mid-film without narrative reason reads as a bug.

## 2. Alpha matte reveal (the workhorse)

```
mask.unshift → creates the visible region
draw text normally underneath, clipped by mask
animate the mask region (position, scale, or path) — never the glyphs
```

- Vertical band per word (Arabic) or per line: sweep from the reading start edge.
- Give the mask a **shape**: hard edge, soft 4–8 px feather, torn paper, ink bleed. Softness is a
  material decision, not a default.
- A matte that moves faster than the eye can track (~>2 frame-widths/s at 1080) reads as a wipe,
  not a reveal; slow it down or hold the end.
- Feather quality is visible at 25 % scale — check the thumbnail, not just 100 %.

## 3. Path trim + head marker

- Measure the path (analytic arc length, not samples of `bezierCurveTo` guesses).
- Draw `t` underlines by trimming `[0, s(t)]`; place the head marker at `s(t)` with the tangent at
  that same parameter. If the marker uses a separate parameter, it will drift — the classic tell.
- Underlines and arrows animate **after** their word is legible (delay 0.15–0.35 s) and finish
  0.2–0.4 s later. Simultaneity kills the relation.
- Callouts: leader line grows from the anchor, then the label resolves; the anchor point is
  derived from layout, not hand-placed pixels.

## 4. Stagger (range delay)

```
delay(i) = start + i * stagger
```
- Word stagger for Arabic: 60–110 ms at 24–32 fps (2–3 frames) reads as a phrase; >150 ms reads
  as a list being read out.
- Use an eased distribution for long runs (`delay = start + (i/n)^1.4 * total`) so the tail does
  not feel mechanical.
- Stagger the *whole build*, not only entrances: masks, underlines, ornaments, shadows can share
  the same delay map with different phases.
- Reverse stagger (last word first) is a strong accent — use once.

## 5. Baseline wave / ripple

Offset per unit using a travelling sine: `y(i,t) = A * sin(2π f t − i * φ)`, with `A` ≤ 6 % of
cap height and `A` decaying to zero by the end of the shot. Wave on lines is calmer than on
words; waves on characters are Latin-only.

## 6. Kinetic typography (rhythm, not decoration)

- Rhythm policy decides everything: `voice-locked` (entrances on speech onsets),
  `pulse-grid` (on musical beats), `syncopated-holds` (deliberate silence + hit),
  `breath-first` (hold on a poster frame), `accelerando-funnel` (gaps shrink toward the turn).
- One word can own a full frame; the drama is internal timing (settle, hold, exit direction).
- Never animate the whole line and call it kinetic type. Kinetic type means words are individually
  timed and the composition reacts to them (size jump, reflow, position change).
- Layout transitions between phrases are their own mechanic: reflow with real correspondence
  (a word that moves, keeps its identity), not a crossfade of two line-ups.

## 7. Copy integrity at all times

- Approved copy is byte-exact; never re-word, re-vowel or drop an alif to fit.
- A temporary copy change (scramble, glitch text, code-typing) requires explicit permission and is
  Arabic-illegal by default.
- Numbers inside RTL lines are isolated runs; declare the numeral system once.
- After any bidi isolation, re-check line order — the most common silent bug.

## 8. Acceptance tests for text

1. frame-by-frame at the reveal start: no half-shaped or disconnected glyph.
2. still at 100 % and at 25 %: diacritics clear, no descender collisions, no clipped matte edge.
3. mid-motion: the word being revealed is the word expected (order correct in RTL).
4. underline/head marker: touches the intended glyph and never overtakes the drawing.
5. the claim's honesty: the technique you shipped matches the name you used in reports.
