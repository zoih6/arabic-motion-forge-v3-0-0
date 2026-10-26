# Quality gates — bounded review, honest reporting

Three passes maximum per version. Each pass fixes the top three concrete issues and logs them.
Scores are internal signals; report findings, not vanity numbers.

## Pass 1 — stills and hierarchy (before any motion review)

Look at 4–6 frames: hook (first 2 s), each beat's settled state, one mid-reveal frame.

- [ ] Hook readable in < 1 s at 25 % scale.
- [ ] Hierarchy: exactly one dominant element per shot; the eye lands where the argument needs it.
- [ ] Arabic: joins intact, lam-alef ligature, finals ـه/ـة, diacritic clearance inside the line box,
      bidi numbers isolated, no mid-word letter-spacing.
- [ ] Margins: nothing critical in the platform UI band or within 6 % of the edge.
- [ ] Contrast and legibility over imagery.
- [ ] Asset edges: no halos, no cut-out leftovers, shadows follow the light azimuth.
- [ ] Every visible element has a role written in `layer-plan.json`.

## Pass 2 — motion (draft render, muted)

- [ ] Not a slideshow: run the anti-slideshow audit below.
- [ ] Independent layers move independently; no group moving as one poster.
- [ ] Three-stage timing visible: off-state → action → settle/hold.
- [ ] Staggers not accidental; two groups never start together without a reason.
- [ ] Camera moves motivated; nothing pushes on every shot.
- [ ] Transitions carry a relation; presets replaced by designed moves.
- [ ] Text enters faster than it can be read; holds long enough to read (read-time math).
- [ ] Reveal mid-frames show the mechanism working (mask edge, marker at its parameter).
- [ ] Accents land on onsets/beats within ±1 frame.

## Pass 3 — the encoded file (not the design)

- [ ] Frame count = `round(duration × fps)`; fps as fraction equals intent.
- [ ] PTS monotonic, constant increments; no duplicate/dropped frames at part joins.
- [ ] Full decode with no errors.
- [ ] Audio present, duration parity ±1 frame, measured LUFS and true peak reported.
- [ ] No frame flashes at part boundaries (concat artifacts).
- [ ] Deliverable copies exist at the requested size(s), hashes recorded.

## Anti-slideshow audit (run on the draft)

Per 10 s window: longest still ≤ 3 s · flat single-motion ratio ≤ 55 % · layered ratio ≥ 45 % ·
≥ 1.5 motion bursts. Failure = re-choreograph, never "re-encode with a filter".

Two human-grade checks that beat any metric:
1. Name what is moving in three random mid-shot frames.
2. Watch once muted; describe the film in one sentence and check it matches the brief.

## Honesty ledger (include in the final report)

Always state:
- what was measured (frames, fps, PTS, decode, LUFS, true peak, hashes);
- what was **not** verified (you cannot hear the mix; you may not have inspected every frame; no
  device/broadcast test was performed);
- what is an equivalent rather than a native tool (masked reveal vs. After Effects' effect, canvas
  compositor vs. 3D renderer, word reveal vs. typewriter);
- anything assumed in `spec.md` that is still unconfirmed.

## Definition of done (per project scale)

| Scale | Must be true |
|---|---|
| quick (≤30 s, single message) | spec-lite, one still pass, one draft motion pass, verified final file, report with limits |
| full | all three passes, approved preview or explicit waiver, full verification, provenance complete, journal closed with `next:` |
| client/broadcast | full + second reviewer pass + human audio listen + device or platform upload check (the human does the last two) |

## Session hygiene (quality is also not hanging)

- Background renders + waits on log patterns, never foreground grind; one renderer at a time.
- Sample tool outputs (`head`, counts, hashes); full logs go to files with a path reported.
- Journal every milestone; the tail is the resume point.
- Cap deliverables: light copy ≤ 25 MiB and high copy ≤ 50 MiB into `deliverables/`; masters outside
  the user's workspace. Check free space before any write > 2 MB.
- Never start a duplicate render because the first "looked hung" — read the log.
