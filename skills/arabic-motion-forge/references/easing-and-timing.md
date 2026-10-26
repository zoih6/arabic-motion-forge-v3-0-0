# Easing & timing — numbers you can defend

Easing is a decision about mass and intent, not a decoration at the end of a tween.

## 1. Core curves

| Name | cubic-bezier | Use for | Avoid for |
|---|---|---|---|
| linear | `0,0,1,1` | ticker loops, constant-rate scroll, mechanical rotation | entrances/exits (reads cheap) |
| ease-out (standard entrance) | `0.16,1,0.3,1` | arrivals that settle, UI-standard motion | departures |
| ease-in (standard exit) | `0.7,0,0.84,0` | things accelerating away | arrivals |
| ease-in-out | `0.65,0,0.35,1` | repositions already on screen | entrances needing instant presence |
| smooth (smootherstep) | `t³(6t²−15t+10)` | matte sweeps, blends, camera moves | short, snappy elements |
| editorial snap | `0.2,0,0,1` | typographic cuts, poster energy | long travels (feels abrupt) |
| soft mechanical | `0.4,0,0.2,1` | product/instrument motion | emotions, organic material |

Speed graph vs value graph: a "speed" curve is the **derivative** of the value curve. If you want
constant speed into a stop, ease the value; if you want a peak mid-travel, shape the value curve
with two keys. Do not copy a speed graph and call it a value graph.

## 2. Duration by travel distance

| Element | Duration |
|---|---|
| micro reaction (marker, dot) | 120–180 ms |
| small element (label, chip) | 180–260 ms |
| word / small block | 260–400 ms |
| headline build (per phrase) | 400–700 ms |
| card or large block travel | 500–900 ms |
| camera move (push/drift) | 1.2–3.0 s, settles before text |
| transition (designed) | 300–700 ms; whip 180–260 ms |
| matte sweep across a line | 350–700 ms |

Same distance in a vertical 9:16 frame feels slower than in 16:9 — reduce by ~10 % if the element
travels along the long axis.

## 3. Stagger tables (in seconds unless noted)

| Unit | Stagger | Notes |
|---|---|---|
| words (Arabic headline) | 0.06–0.11 | phrase rhythm; 2–3 frames at 24–32 fps |
| lines | 0.12–0.24 | longer than word stagger or it reads as one block |
| list items / chart bars | 0.04–0.08 | faster: quantity over emphasis |
| characters (Latin only) | 0.02–0.05 | Arabic: forbidden |
| secondary layers (underline, shadow) | 0.15–0.35 after the word resolves | independent key, not parented delay |

Rules: total build time = `n * stagger + single-duration`; keep the whole phrase under ~1.2 s.
Use eased distribution for n > 6 (`(i/n)^1.3`), and never let two independent groups start at the
same instant by accident.

## 4. Holds and read time

- Minimum hold for a short headline: **0.6 s** after it finishes building; for a number or a claim
  the viewer must register: **0.9–1.2 s**.
- Read time estimate: Arabic ≈ 1 char / 70 ms for comfortable reading of a headline (≈14 char/s),
  plus 0.4 s settle. Show the math when a duration is tight.
- Stillness > 3 s in a film is a decision that must be visible in the plan (a breath before the
  turn), never an accident.
- A hold is not a slow tween: the element must be exactly static.

## 5. Frame quantization

- Cut points and audio cues land on `k / fps` seconds.
- At 24–25 fps: motion over 1 frame reads as a snap; over 2–3 frames as quick; 6+ as smooth.
  Entrances rarely need more than 8–12 frames.
- Posterize-time (8–12 fps) applies to element transforms only; camera stays smooth.
- Compare fps as fractions/floats (`32/1 == 32.0`), never as strings.

## 6. Motion blur (temporal sampling)

Shutter 180° → samples spread over half a frame interval. Implementation: average N sub-frame
evaluations of the same `seek(t)`, N = 4–8 for fast moves, 2–3 for slow. Cost scales linearly;
enable per shot, not globally. Document N and the shutter angle in the shot notes.

## 7. Timing chart (write it per shot)

Before writing code, sketch the shot as bars:

```
layer          | t=0 ────────────────────────────── 4.0s
background      ▓▓▓▓▓▓▓▓ (drift 0–4)
headline-1          ███ (matte 0.6–1.3)
headline-2              ███ (matte 1.0–1.7)
underline                        ████ (trim 1.5–2.1)
photo                 ██████████ (slide 0.9–2.4 + settle)
shadow                ███████████ (independent, 0.95–2.5)
accent-dot                   ██ (pop 2.0–2.2, spring)
hold                                          ░░ (2.5–4.0)
```
If two bars start and end together without a reason, the shot will look flat. If every bar starts
at the same time, you have a slideshow.

## 8. Easing anti-patterns

- One easing for every layer (everything feels like it is on the same rail).
- `linear` on any entrance.
- Overshoot on body copy the viewer must read immediately.
- Ease-in on content the viewer is waiting for (it delays the payoff).
- Bounce on interfaces or data (a number that bounces is no longer a number).
- Exponential "cinematic" slowdowns on short elements (<200 ms) — the curve becomes invisible and
  the motion just feels late.
