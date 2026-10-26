# Migration v2.4.1 → v3.0.0

v3.0.0 is a **structural** release: the repository stopped being an application and became an Agent
Skill. This is a breaking change for anyone who called v2 tools; it is a simplification for anyone who
used v2 as a skill.

## What was removed, and where its knowledge went

| Removed (v2 path) | Files | Why | Now lives in |
|---|---|---|---|
| `tools/` (intake, direct, render, gate, deliver, quota, journal, transcribe, export_ae, doctor, …) | 30 | implemented policy the agent should decide | instructions: `spec-and-reframe`, `creative-direction`, `implementation`, `quality-gates`, `platform-delivery` |
| `runtime/` (`core.mjs`, `canvas.mjs`, `spatial.mjs`) | 4 | a frozen library the agent could not adapt | math and params: `motion-mechanics-physics`, `motion-mechanics-space`, `easing-and-timing` |
| `vendor/ai-film-studio/` | ~200 | full third-party film pipeline, unrelated to a lean skill | narrative essentials: `story-structure`; install it separately if you need it |
| `vendor/motion-reel/`, `vendor/primitives/` | ~30 | vendored engines with licence bookkeeping | engine choice + licence rules: `implementation` §1, §8 |
| `catalog/motions.json`, `motions.csv` | 2 | 104 labels ≠ 104 techniques; unreadable to an agent under budget | alias table + mechanisms: `motion-mechanics-text` |
| `catalog/dictionaries/*.json` (6) | 6 | good content, wrong format for progressive disclosure | tables in `creative-direction`, `arabic-typography`, `motion-craft`, `easing-and-timing` |
| `catalog/libraries.lock.json`, `compatibility-baseline.json`, `capabilities.json` | 3 | pinned states of a world that no longer exists | licence/honesty rules: `implementation` §8, `after-effects-equivalence` |
| `reference/recipes/*.md` (33) | 33 | near-identical boilerplate per label | three mechanism files |
| `reference/*.md` (guides, duplicates) | 16 | duplicated the skills with different wording | merged into the references above |
| `skills/*/SKILL.md` (16 sub-skills) | 16 | 16 descriptions in context, chained loading | `SKILL.md` route table + references |
| `workflows/` (route, production, vendor-bridge) | 3 | nested routing; vendor coupling | `workflow` (single phase pipeline) |
| `schemas/`, `templates/*.json`, `adapters/` | 6 | schemas as JSON that nothing validated | `assets/templates/` (brief, spec, style, layer-plan, journal) |
| `tests/` | 5 | tested v2 code | acceptance tests written as checklists in `quality-gates`, `arabic-typography` §9 |
| `research/` (reports, contact sheets, licences) | 22 | session archaeology | distilled rules; nothing to re-read |
| `lab/index.html`, `examples/vertical-showcase/` | 11 | 336 KB lab + 80 s demo film | a worked *shape* in `assets/templates/` (no scene code to copy) |
| `install.sh`, `tools/install_*`, `INSTALL.md` | 4 | installer for an application | three copy-paste commands in `README.md` |
| `kit.json`, `package.json`, `skill_launcher.md`, `.agents/` | 4 | four manifests describing the same thing | `.claude-plugin/plugin.json` + `SKILL.md` frontmatter + `AGENTS.md` |
| `AGENTS.md` (17 KB contract), `CLAUDE.md` (duplicate) | 2 | the contract is now `SKILL.md` | `SKILL.md`; root files are thin pointers |

**Net: 437 files → 37 files** (34 text files + 3 font files), and ≈4.6 MB → ≈600 KB. No rule that
shapes output quality was lost — only the machinery that executed it.

## What changed in behaviour

| v2 | v3 |
|---|---|
| First touch runs `tools/intake.py` | Agent greets and asks the same five questions itself, then writes `brief.json` |
| `new_project.py` scaffolds the project | Agent creates the folder from `assets/templates/` |
| `direct.py` generates directions, `check_directions.py` validates | Three directions on eight axes with a written refusal; the user picks |
| `render.py` + `runtime/*.mjs` render | Agent writes the project's own deterministic renderer (`implementation`) |
| `gate.py` aggregates checks | Gates are explicit exit conditions per phase (`workflow`) |
| `motion_audit.py` scores flatness | The same four metrics, stated so the agent measures them itself |
| Vendor bridge to `ai-film-studio` was mandatory | Story structure is native; install the film pipeline separately if needed |
| Installer + compat guarantees for v1 projects | Not applicable — projects are self-contained code |

## If you still need v2 behaviour

It is preserved in git history: `git checkout v2.4.1`. Keep it as a reference implementation, not as
a dependency. Nothing in v3 imports from it.

## Applying this restructure to the GitHub repo

The new tree is a full replacement. From a clone of the old repository:

```bash
# on a branch, with the v3 tree downloaded/overlaid at the repo root
git checkout -b restructure/v3
git rm -r --quiet tools runtime vendor catalog research lab examples tests schemas adapters \
  workflows templates reference skills assets package.json kit.json skill_launcher.md \
  INSTALL.md install.sh CHANGELOG.md .agents
# copy the v3 tree over the working directory, then:
git add -A
git commit -m "v3.0.0: rebuild repository as an instruction-only Agent Skill"
git push -u origin restructure/v3
```

Then tag (`git tag v3.0.0`), publish a release noting the breaking change, and edit the repository
"About" description to say *Agent Skill* rather than *application*.

## Optional extras not carried over

- The 80-second vertical film (`examples/vertical-showcase`) — publish it as a Release asset or a
  demo link in the README if it is still valuable; do not keep it in the skill tree.
- The motion lab (`lab/index.html`) — a nice teaching artifact, but a 336 KB HTML file in a skill
  repository is context bait. Host it as a GitHub Pages demo instead.
