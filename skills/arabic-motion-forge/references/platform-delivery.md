# Platform & delivery — specs, sizes, handoff

## 1. Format table

| Target | Ratio | Pixels | fps | Duration sweet spot | Notes |
|---|---|---|---|---|---|
| TikTok / Reels / Shorts | 9:16 | 1080×1920 | 30 (24 for cinematic) | 7–34 s (up to 90 s) | UI bands top ~8 %, bottom ~12–18 % |
| Instagram feed video | 4:5 | 1080×1350 | 30 | 15–60 s | maximum vertical area in feed |
| Instagram square | 1:1 | 1080×1080 | 30 | 15–60 s | safe margins ≥8 % |
| YouTube | 16:9 | 1920×1080 (or 3840×2160) | 24–30 | any | hook in first 5 s |
| Stories | 9:16 | 1080×1920 | 30 | ≤15 s per card | keep copy clear of both bands |
| Presentation / web hero | 16:9 or 1:1 | 1920×1080 | 30–60 | 5–15 s loop | loop seamlessly if silent |
| DOOH / screen | per venue | 1920×1080 or vertical | 25/30 | 10–15 s loop | no audio, high contrast, long holds |
| Print-grade stills | — | ≥2160 px | — | — | export stills from the master render |

Muted viewing is the default: **design for no audio** first, then let sound add. Captions or
on-screen copy that carries the message are not optional.

## 2. Size math (show it, don't guess)

```
required bitrate (kbps) ≈ target_MB × 8 × 1000 / duration_s        # includes audio
target_MB per quality tier:
  web/social master    8–12 Mbps at 1080p30 (crf 18 is usually better than a bitrate cap)
  WhatsApp / email     4–6 Mbps
  preview/draft        1.5–2.5 Mbps
```
If the user gives a size and no duration, derive duration or quality and write the trade-off in
`spec.md`. Encode with CRF for quality, then compress a delivery copy to hit the cap.

## 3. Codec and container

- H.264 (libx264), `yuv420p`, `+faststart`, AAC 192 kbps 48 kHz — the universal choice.
- H.265/AV1 only when the destination supports it (smaller, slower, worse compatibility).
- ProRes/DNxHR for handoff to an editor; PNG/mov when alpha is needed.
- Never ship a variable-frame-rate file; never ship without an audio track when the platform expects
  one (add silent audio if the film is silent).

## 4. Naming and folders

```
deliverables/
  <project>-9x16-master.mp4        # highest quality, full length
  <project>-9x16-social.mp4        # capped size for upload
  <project>-9x16-poster.jpg        # first frame / hero still
  <project>-contact-sheet.jpg      # 8 panels, review aid
  report.md                        # what was measured, what was not verified
  NOTICES.txt                      # third-party licenses used in the film
```

Version by suffix (`-v2`), never overwrite a delivered file silently.

## 5. Storage discipline

- Masters and intermediates live outside the user's workspace (temp/staging); the workspace receives
  only `deliverables/` copies, ≤25 MiB light / ≤50 MiB high.
- Check free space before any write over ~2 MB; delete intermediates only after the deliverable is
  verified and hashed.
- Sources are small and belong in version control; media does not.
- If a render dies after some parts, the parts must still be usable: keep a per-part manifest with
  hashes so a resumed session rebuilds only what is missing.

## 6. Handoff / publishing

- Ask before publishing. Record destination, privacy and authorisation in `brief.json`.
- Upload, then verify by fetching the published object and comparing size/sha before telling the
  user it is safe.
- Secrets (tokens, cookies, signed URLs) exist only in the environment of one process: never in
  files, git remotes, committed commands, screenshots or chat logs.
- Close with: links, hashes, what is public, and what remains local.

## 7. Platform-specific craft notes

- **Vertical**: one idea per beat, larger type than feels comfortable on a desktop monitor, bottom
  third reserved for UI. Safe margins 6–8 %.
- **Muted autoplay**: the first 1.5 s must communicate without sound; captions sit above the UI band.
- **Loops**: if the film loops, the last frame must cut cleanly to the first (same composition,
  matching energy). Silent loops for DOOH have no text that requires reading time.
- **Thumbnails**: export a still that reads at 320 px wide; the poster frame is part of the design.
- **RTL platforms**: keep progress/UI conventions neutral; do not mirror brand logos.
