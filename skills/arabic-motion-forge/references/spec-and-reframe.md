# Spec & reframe — turning a request into a production brief

A user request is **input, not a spec**. One sentence, three contradictory demands and a
reference video is normal and fine: your job is to make it professional, not to obey its
literal wording. Nothing may be executed before `spec.md` exists.

## Step 1 — extract slots

From the raw message, fill: subject/message, audience, duration, format (w/h/fps), on-screen
copy, audio source and sync intent, style references, deliverables and size budget, publishing,
approval mode, deadline pressure. Mark anything you inferred with `assumed: true`.

## Step 2 — preserve everything

Copy **every** distinct demand into `## User requirements` as a numbered item with a status:

- `preserved` — implemented as asked.
- `converted` — replaced by a compliant equivalent, with the rule that forced the change.
- `corrected` — the demand was factually or technically impossible; state the correction.

Deleting or silently dropping a demand (a word, a photo, a song, an odd detail) is a critical
failure. If two demands are mutually exclusive, keep both and record a trade-off question.

## Step 3 — convert the standing contradictions

| Request | What you do | Why |
|---|---|---|
| "letter-by-letter Arabic animation" | per-word shaped reveals, or mask bands per phrase | connected Arabic breaks when split; shaping is contextual |
| "typewriter for this Arabic sentence" | document the shaped-word reveal you actually implemented | a glyph-prefix typewriter does not exist for shaped Arabic |
| "real 3D, exactly like Cinema4D" | 2.5D multi-plane, or a real perspective camera with planar quads; label the limits | no volumetric renderer; honesty over decoration |
| "make it exactly like <video/link>" | extract 2–4 transferable mechanics; write them down; never import files or identity | learning structure is allowed, copying identity is not |
| "5 MB, 60 seconds, no other specs" | derive bitrate math and show it: `MB × 8 × 1000 / duration ≈ kbps` | a size demand without a duration is unsolvable |
| conflicting duration vs. copy length | compute read time (≈ 12–15 Arabic characters/second for comfortable reading, plus 0.4 s settle) and show the trade-off | readability is a hard constraint |
| no facts for on-screen numbers | mark `illustrative`, or research them with sources | an invented statistic is a lie, not a style |
| "just do it, don't ask" | proceed, but write the assumptions block so one word can veto | silence equals consent for assumptions, not for contradictions |

## Step 4 — write `spec.md`

Use `assets/templates/spec.md`. Sections (each ≥2 sentences, or explicitly "not applicable"):

```markdown
# Spec — <project>
## Objective
## Confirmed understanding      (restate intent in their words; what changed and why)
## Constraints & format
## Content & copy
## Motion & style
## Deliverables
## Assumptions (veto in one word)
## User requirements           (numbered: preserved | converted | corrected)
## Refusals & corrections      (each with the governing rule)
```

## Step 5 — get consent cheaply

Present the spec as a short message: the objective, the format, the two or three decisions that
matter, and the assumptions list. A single "تمام" or "ok" is approval. "Do it" at the start is
already approval: proceed, and note the waiver.

## Step 6 — sync downstream

`brief.json` mirrors the spec's machine-readable parts. When the user edits copy, update
`copy.txt`, recompute the hash, and mark any earlier render as stale in `journal.md`.

## Anti-patterns

- Interview loops: more than one round of questions after the first contact is a failure unless
  a gate genuinely cannot pass.
- Asking the user to write prompts, choose codecs, or pick easing curves.
- Silently "improving" the message, the copy or the brand voice.
- Turning a vague brief into a generic template because it was vague. Vagueness is a design
  problem: choose a stance, justify it in three lines, mark it vetoable.
