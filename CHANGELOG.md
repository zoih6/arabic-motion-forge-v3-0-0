# Changelog

## 3.0.0 — 2026-10-25

**Rebuilt as an instruction-only Agent Skill.** Breaking change: the repository no longer ships
tools, a runtime, vendored engines, catalogs, tests or a demo film. Full rationale and a file-by-file
disposition in [`docs/MIGRATION-v3.md`](docs/MIGRATION-v3.md).

Added
- `skills/arabic-motion-forge/SKILL.md` — single hub: eight laws, route table, ten-phase pipeline (0–9) with
  exit gates, reporting contract.
- 16 self-contained references, one hop from the hub: `workflow`, `spec-and-reframe`,
  `creative-direction`, `story-structure`, `motion-craft`, `motion-mechanics-text`,
  `motion-mechanics-physics`, `motion-mechanics-space`, `easing-and-timing`, `arabic-typography`,
  `audio-and-sync`, `assets-and-provenance`, `implementation`, `quality-gates`, `platform-delivery`,
  `after-effects-equivalence`.
- `assets/templates/` — brief, spec, style, layer plan and journal shapes the agent instantiates per
  project.
- `skills/arabic-motion-forge/assets/fonts/` — IBM Plex Sans Arabic (Regular, SemiBold, OFL 1.1).
- `docs/ARCHITECTURE.md`, `CLAUDE.md` maintenance contract, plugin manifests for Claude Code.

Removed
- `tools/` (30 scripts), `runtime/` (4 modules), `vendor/` (234 files), `catalog/` JSON dictionaries
  and motions catalog, `reference/recipes` (33), 16 sub-skills, `workflows/`, `schemas/`, `adapters/`,
  `tests/`, `research/`, `lab/`, `examples/`, `install.sh`, `INSTALL.md`, `kit.json`, `package.json`,
  `.agents/`.
- Contract duplication across `AGENTS.md` / `CLAUDE.md` / sub-skills: the contract now lives once, in
  `SKILL.md`.

Changed
- First-contact intake, project scaffolding, direction generation, gating, rendering and QA are now
  **instructions the agent executes**, not scripts it invokes.
- Identity/narrative and Arabic/technical knowledge were merged and rewritten in imperative English
  with concrete numbers; Arabic user-facing wording (intake questions, on-screen copy rules) retained.

## 2.4.1 and earlier

See git history (`git log --oneline v2.4.1`). v2.x was a full application: deterministic Canvas
runtime, 30 CLI tools, 16 skills, 104-entry motion catalog, vendor film-pipeline bridge, demo film.
