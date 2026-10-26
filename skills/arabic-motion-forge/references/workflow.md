# Workflow — phases, gates and artifacts

Run the phases in order. Each phase ends with a gate: an explicit, checkable condition. If a
gate fails, fix inside that phase; do not carry debt forward. When the work is trivial, several
phases collapse — but the artifact and its gate still exist in a compressed form.

| Phase | Input | Artifact you must write | Exit gate |
|---|---|---|---|
| 0 intake | user request, answers | `brief.json` | format (w/h/fps), duration, audio source, approval mode, audience, message all known or explicitly assumed |
| 1 reframe | raw request | `spec.md` | every demand preserved or converted with a reason; no execution of a literal contradictory ask |
| 2 research | topic, facts, style refs | `research.md` | ≤8 sources, every on-screen fact has a URL and a retrieval date, findings are mechanics not vibes |
| 3 direction | spec + research | 3 directions, one approved in `style.json` | the three differ on ≥4 axes; the chosen one names a signature move and a refusal |
| 4 style lock | approved direction | `style.json` (palette, type system, material, camera grammar, motif), `copy.txt` + sha | text/voiceover is final; no later wording change without re-hashing |
| 5 plan | story + style | `layer-plan.json` | every layer has id, function, depth, parent, channels, timing keys, reveal method |
| 6 proof | hardest shot | 2–4 s test clip + notes | Arabic shaping correct at full size and 25%; matte edges clean; text readable mid-motion |
| 7 build | plan | `src/` | `seek(t)` pure; still frames at start/mid/end correct; no console errors |
| 8 review | draft render | `journal.md` entries + contact sheet | layered motion verified, ≤3 concrete issues per pass, fixes logged |
| 9 deliver | approved cut | final MP4 + verification values | frame count, fps, PTS, decode, loudness, hashes all measured and reported |

## Phase notes

**0 intake.** Do not interview the user about technical choices. Ask only what changes the
design: message, audience, duration, platform, audio, taste. Derive everything else from the
platform defaults (9:16 1080×1920@30 for reels, 4:5 1080×1350 for feed, 16:9 1920×1080 for YouTube).

**1 reframe.** The failure this prevents: executing a one-line, self-contradictory request
literally and shipping broken Arabic, an unreadable duration or a fake claim. See
the reframe rules: extract slots, preserve every demand, convert contradictions with a written reason.

**2 research.** Skip when the topic is familiar and nothing appears on screen as a claim.
Never let research block production: if the network is unavailable, write `mode: no-network`
in `research.md` and proceed from craft knowledge. Research feeds mechanics, not decoration.

**3 direction.** Three directions, each a spine of decisions (story device, metaphor,
material, type architecture, camera, rhythm, accent). Present them compactly: id, one-line
spine, first frame, signature move. The user picks; if the user says "you choose", pick one,
justify in three lines, and say that you chose.

**4 style lock.** Lock the copy before animating it. Compute `copy_sha256` over the exact
bytes of `copy.txt` and record it in `brief.json` and `style.json`. Any later wording change
re-hashes and invalidates downstream approval.

**5 plan.** This is the design document that separates a designer from an animator. One entry
per independently moving thing, with the channel list restricted to what meaning requires.

**6 proof.** Build the hardest thing first — the longest Arabic headline, the trickiest matte,
the fastest transition. A proof clip is 2–4 seconds, draft quality, and answers one question.

**7 build.** Element state is a function of `t`. Precompute per-shot pure functions; compose
shots by time ranges. Keep the scene file per shot so a fix rebuilds one file, not the film.

**8 review.** Three passes maximum, scoped: (1) stills and hierarchy, (2) motion in a draft
render, (3) the encoded file. Each pass fixes the top three concrete issues. See
the three-pass review: stills, motion (muted draft render), encoded file — top three issues each.

**9 deliver.** Verify the encoded output, not the design intent. Copy deliverables into the
project's `deliverables/` folder at a sensible size, then report what was and was not verified.

## Resuming a session

1. Read `journal.md` tail (last ~20 lines) and `brief.json` / `style.json`.
2. State the resume phase in one line.
3. Trust the files over your memory of the conversation.
4. Never re-run a completed render whose inputs and hash are unchanged; never re-ask settled
   questions. If the previous session hung mid-command, diagnose the log, do not blindly
   repeat the command.

## Journal format (append-only, cheap)

```
## 2026-10-25 14:02 — phase 7 build
- wrote src/scene-03.mjs (headline build + underline draw)
- proof: Arabic matte clean at 100% and 25% (still-03a.png)
- next: wire audio onsets into scene-04, then draft render 0–12 s
```

The `next:` line is what makes a crashed session cost two reads instead of a re-do.
