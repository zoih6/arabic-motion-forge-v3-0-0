# Physics mechanics — springs, weight, impact, follow-through

Physics in motion design is a **behaviour model**, deterministic and closed-form. Never run a
frame-dependent simulation in the render path.

## 1. Analytic spring (the default for settle motion)

Damped harmonic oscillator, solved in closed form so `state(t)` is pure:

```
ω0 = sqrt(k/m)            // natural frequency (rad/s)
ζ  = c / (2*sqrt(k*m))    // damping ratio
x(t) = 1 - e^(-ζω0 t) * ( cos(ωd t) + (ζω0/ωd) sin(ωd t) ),   ωd = ω0*sqrt(1-ζ²)
```

| Feel | ζ | ω0 (rad/s) | Use |
|---|---|---|---|
| crisp UI-ish | 1.0 | 18–22 | small elements, labels, markers |
| confident settle | 0.7–0.8 | 14–18 | text blocks, cards, badges |
| playful overshoot | 0.45–0.55 | 12–16 | one hero element, never body copy |
| heavy mass | 0.85 | 8–11 | large objects, product reveals |

Rules: one overshoot maximum unless the object is cartoon-elastic; overshoot amplitude is a
physical statement about mass — do not add it to typography that must be read instantly.
Sampling must be by `t`, not by time-since-last-frame.

## 2. Anticipation → action → settle

The most under-used craft move. Sequence for one element:

```
t0      reverse micro-move (−3 to −8 % of travel, 80–140 ms)
t0+Δ    main travel with ease-in then ease-out
t1      overlap into overshoot ≤ 8 % (only if mass justifies)
t1+Δ    settle with a 2–3 frame hold before anything else happens
```

Anticipation applies to the whole choreography, not just single objects: the film can lean back
before the reveal.

## 3. Ballistic / gravity

```
y(t) = y0 + v0 t + ½ g t²         bounce: v ← −v * restitution (0.35–0.6)
```
- Use for drops, throws, paper falling, objects landing. Not for text entrances (text has no
  gravity contract; a falling headline reads as a mistake unless it is a physical poster).
- Prevent ground tunnelling analytically: solve for contact time rather than detecting a crossing.
- Squash & stretch on contact: keep volume approximately constant (`sx * sy ≈ 1`), amplitude 6–14 %
  for elastic bodies, 0 % for rigid ones.

## 4. Inertia and drag

```
v(t) = v0 * e^(−λt)        offset(t) = (v0/λ)(1 − e^(−λt))
```
For flick continuations, gesture-driven reveals and "thrown" elements. Record the input path; in
an export there is no live interaction, so the gesture is authored, not simulated.

## 5. Follow-through and secondary motion

- The lead element defines the path; followers lag by 1–4 frames and overshoot slightly more.
- Amplitude decays with hierarchy: child ≈ 0.4–0.7 × parent, grandchild ≈ 0.3 × child.
- Secondary elements (underline, ornament, shadow) start on their own key, not on the parent's.
- A shadow that must react to height needs its own channel pair (offset + blur), derived from the
  same elevation value the object uses.

## 6. Recoil, wobble, jiggle, shake

Local damped oscillation around a rest state, amplitude ≤ 4 % and duration ≤ 0.4 s, triggered by
an event (impact, landing, hit). Wobble has a reason or it is removed. Screen shake is a last
resort and must never exceed 0.3 % of frame size, and never on a shot the viewer must read.

## 7. Wiggle / drift (organic noise)

Use seeded value noise, not random:

```
n(t) = valueNoise(seed, t*freq) * amp
```
Fixed seed → identical output every render. Amplitude 0.5–2 % for drift/float; frequency 0.2–0.6
Hz for organic breathing, 1–3 Hz for tension. Anything amplitude >3 % becomes a shake, not a drift.

## 8. Weight storytelling cheat sheet

| Feeling | Model |
|---|---|
| feather-light | ζ 0.4, ω0 20, small travel, no squash |
| confident product | ζ 0.75, ω0 14, 1 overshoot ≤6 % |
| heavy industrial | ζ 0.9, ω0 8, long settle, ground contact with small squash |
| elastic/toy | ζ 0.35, ω0 16, squash 12 %, bounce on contact |
| mechanical/stepped | no spring at all: quantize time to 8–12 fps |

## 9. Determinism checklist for physics

- No `Math.random`, no `performance.now()`, no accumulating state.
- Fixed numeric seeds; document them (`style.json.physics.seed`).
- Closed-form or precomputed tables, evaluated at `t`.
- Test: request `tA, tB, tA`; the two `tA` frames must be byte-identical.
- Frame-rate honesty: physics tuned at 30 fps must not be re-timed by sampling at 60 without
  re-tuning parameters (they are in seconds, not frames — but stagger values often are frames).
