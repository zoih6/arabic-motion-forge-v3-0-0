# Journal — <project-id>

Append-only. One block per milestone. Keep the `next:` line truthful: a new session reads the
tail and continues from it in two reads.

## YYYY-MM-DD HH:MM — phase 0 intake
- answers: <platform, duration, audio, style>
- assumptions: <…>
- next: author spec.md

## YYYY-MM-DD HH:MM — phase 1 reframe
- spec.md written; 1 conversion (letter-by-letter → shaped word reveals)
- next: build proof clip for the longest headline

## YYYY-MM-DD HH:MM — phase 7 build
- wrote src/scene-03.mjs; seek(t) deterministic (digest t check passed)
- proof stills: deliverables/still-03a.png (100 %), still-03b.png (25 %)
- next: wire audio onsets into scene-04, then draft render 0–12 s

## YYYY-MM-DD HH:MM — phase 9 delivery
- final.mp4: 1080x1920, 30 fps, 27.4 s, 810 frames, PTS OK, decode OK, -14.2 LUFS, TP -1.8 dBTP
- sha256 <hash>; copies in deliverables/ (18 MiB social, 44 MiB master)
- unverified: audio by ear, no device playback test
- next: user review of deliverables/<file>.mp4
