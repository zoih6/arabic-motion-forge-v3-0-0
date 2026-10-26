# CLAUDE.md — maintaining this repo

This repo is a single Agent Skill. Keep it that way.

- `skills/arabic-motion-forge/SKILL.md` is the hub: **< 300 lines**, laws + route table + gates only.
  Detail belongs in `references/`.
- Every reference must be **self-contained** and linked **directly from SKILL.md**. Never let a
  reference send the reader to another reference (one hop only) — agents half-read chains.
- Frontmatter: `name` kebab-case ≤64 chars; `description` ≤1024 chars, no XML, must state *what* and
  *when*; include Arabic trigger words.
- No `scripts/`, `runtime/`, `vendor/`, generated catalogs, demo media or test harnesses. If you feel
  the urge to add a script, add an instruction instead — the agent writes the code.
- Prose rules: imperative voice, concrete numbers (durations, px, dBTP), tables over paragraphs,
  no filler, no explaining what a capable agent already knows.
- Anything a project produces (renders, media, reports) never enters this repo.
- Version bumps: `SKILL.md` behaviour change → minor; file-structure change → major. Keep
  `.claude-plugin/plugin.json` and `CHANGELOG.md` in sync.
- Before committing: `wc -l` the skill and references; confirm no reference is orphaned, no link is
  broken, and no file exists that `SKILL.md` does not justify.
