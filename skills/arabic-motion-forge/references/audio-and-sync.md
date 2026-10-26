# Audio & sync — the file is the truth

Source of truth is the audio **file**, measured. Never guessed, never re-performed.

## 1. Handling user audio

- User-supplied voice or music is canonical: never regenerate, time-stretch, or overwrite it.
- If the user names a time range of a recording, that range is the reference — do not substitute a
  clip that happens to contain similar words with different delivery.
- Record file name, duration, sample rate, channels, sha256, and licence/source in
  `assets/manifest.json`.
- Ducking happens only if asked. When a sung insert replaces a spoken passage, define exactly:
  source in/out, whether the narrator stops, what text the song replaces, and where narration
  resumes.

## 2. Word and beat timing (how to get it, honestly)

- Use a real transcription/alignment tool when available (e.g. `whisper`/`faster-whisper` CLI, or
  forced alignment against the approved script). Record tool name + version + model in the journal.
- **Raw ASR text never goes on screen.** The approved `copy.txt` owns the glyphs; the timing map
  owns the time.
- Confidence: mark words below ~0.55 confidence as untrusted and verify by listening… which you
  cannot do. So: prefer alignment against the approved script, report the confidence, and say the
  limit out loud.
- Beats: derive onsets/energy from the actual file (`librosa`, `aubio`, or ffmpeg
  `silencedetect` + an energy envelope). Record the method in the journal.
- Cache results keyed by `(audio sha256, tool, language)`; store as `audio-map.json`:

```json
{ "source": "vo.mp3", "sha256": "...", "fps": 30,
  "words": [{"t0": 0.42, "t1": 0.71, "text": "الحركة", "conf": 0.91}],
  "beats": [0.5, 1.0, 1.5], "energy": [[0.0, 0.3], [0.5, 0.8]] }
```

## 3. Locking motion to sound

- Entrances land on a word onset or a beat multiple; assert tolerance ±1 frame (±2 for long words).
- Narration-driven films: build the timeline from word times; music only ducks under voice.
- Music-driven films: build from the beat grid; accent hits on onsets, not on a decorative pulse.
- Never place a cut on a frame where the audio has an unresolved consonant or a breath — the cut
  will feel late even if it is mathematically on the beat.
- Silence is a cue: a 300–600 ms hole before the turn is one of the strongest tools you have.

## 4. Levels and delivery targets

- Sample rate 48 kHz; keep the master untouched, build the mix with a filter graph and keep the
  splice map.
- Loudness measured **on the encoded file**: target ≈ −14 LUFS integrated, true peak ≤ −1.5 dBTP.
  Measure with `ffmpeg -af ebur128` or two-pass `loudnorm=print_format=json`, and report the
  measured numbers.
- Dialogue intelligibility: voice sits above music; duck 3–6 dB under speech if the user asked.
- Check splices for clicks: an energy spike > 12 dB at a boundary means a 2 ms fade in/out is
  missing.
- Never normalize by ear-guess; always state the measured values and deviations.

## 5. Muxing and A/V integrity

- Mux audio in one pass with a continuous stream. Do not concatenate separately-primed AAC parts
  and expect sample-accurate sync — encoder priming shifts each part.
- Cut points are frame-quantized (`k/fps`); audio edits may be sample-accurate — record the small
  delta explicitly instead of pretending it is zero.
- After muxing: verify duration parity between video and audio (±1 frame), no negative PTS, and
  decode the whole file once.

## 6. What you must tell the user

You cannot hear. State it plainly, then give numbers: LUFS, true peak, duration delta, onset deltas,
number of splices checked, and which passages a human should listen to before publishing. A
"sounds great" from an agent is a lie even when the mix is fine.
