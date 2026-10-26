# Architecture — why the repo looks like this

## The problem with v2

v2.4.1 was a well-engineered *application* wearing a skill's clothes: 437 files, of which 234 were
vendored third-party source, 30 were Python tools, 4 were runtime modules, plus tests, a 336 KB
interactive lab, a 92 KB motion catalog, 22 research artifacts and an 80-second demo film. Every
session that triggered the skill paid for decisions that had already been made, and the agent's job
was reduced to running scripts — the opposite of judgment.

Three structural faults:

1. **Machinery where instruction belongs.** `intake.py`, `direct.py`, `render.py`, `gate.py`
   implemented policy. When the policy needed to change, it changed in code that the model could not
   read or adapt. A capable agent with a clear contract writes that code in minutes, per project,
   correctly.
2. **Duplication.** `AGENTS.md` + `SKILL.md` + 16 sub-skills + `reference/*.md` + `catalog/*.json` +
   `schemas/` said the same things in four places, with different levels of detail. Duplication is
   how a skill rots.
3. **Nested references.** `SKILL.md` → `workflows/route.md` → `skills/x` → `reference/y` →
   `catalog/z.json`. Agents half-read chains; the documented behaviour was never the executed one.

## The structure that replaced it

The [Agent Skills specification](https://agentskills.io/specification) and Anthropic's
[authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
prescribe three levels: metadata always in context (~100 tokens), `SKILL.md` on activation
(< 5k tokens), bundled files only on demand. This repo now does exactly that and nothing more.

```
Level 1  name + description             ≈ 100 tokens, always
Level 2  SKILL.md                       ≈ 200 lines: laws, routes, gates, reporting
Level 3  references/*.md (16 files)     opened one at a time, each self-contained
         assets/templates/*             copied into a project, never read for instructions
         assets/fonts/*                 binary, zero context cost
```

Design rules applied:

| Rule | How it is honoured |
|---|---|
| SKILL.md < 500 lines | 200 lines: the eight laws, the route table, the phase pipeline, reporting. |
| One hop to any file | Every reference is linked from `SKILL.md`; no reference links to another. |
| Flat, self-contained references | Each file states its own rules, numbers and acceptance tests. |
| Descriptions that route correctly | Frontmatter names the concrete artefacts and includes Arabic trigger words. |
| No time-sensitive content | No tool versions, no URLs that will rot, no changelog in the skill body. |
| Scripts only when execution beats judgment | None. Encoding/transcription commands are given as commands to run, not code to trust. |

## Content decisions

- **Knowledge kept**: Arabic shaping law, motion decomposition, physics parameters, easing numbers,
  camera projection math, continuity checks, quality gates, platform specs, AE equivalence, honesty
  rules, session hygiene.
- **Knowledge compressed**: 104 motion labels → one alias table + 3 mechanism files (labels are not
  tools); 33 recipe files of near-identical boilerplate → the actual mechanisms; 6 dictionary JSONs →
  tables inside the direction, typography and craft references.
- **Removed**: vendor trees, runtime, Python tools, tests, catalogs, lab, schemas, adapters, research
  artifacts, demo film, install scripts. Rationale per item in `MIGRATION-v3.md`.
- **Added**: `story-structure` (narrative was buried inside a vendor skill), `platform-delivery`
  (format/size math), journal and layer-plan templates so a project's state is created by the agent
  from a documented shape rather than a generator script.

## Consequences you should expect

- First render in a fresh project takes slightly longer: the agent writes the render harness. That
  harness is then *the project's own*, versionable and fixable.
- Nothing "just runs". There is no installer to break, but also no warm-up: dependencies
  (Chromium, ffmpeg, whisper) are chosen and installed per project, with licences checked.
- Quality depends on the model following instructions. Mitigation: laws are imperative, gates are
  checkable, and the honesty/reporting contracts are stated as outputs the user sees.

## Maintenance contract

`CLAUDE.md` holds the rules for editing this repo (line limits, one-hop rule, no-scripts rule,
versioning). Any addition must pass one question: *does this teach judgment, or does it replace it?*
If it replaces it, it does not belong here.
