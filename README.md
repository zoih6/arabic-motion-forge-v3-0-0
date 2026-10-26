# Arabic Motion Forge

**نظام تعليمات (Agent Skill) لإنتاج موشن جرافيك عربي أصيل** — طباعة عربية سليمة، توقيت طبقي،
حتمية في التصيير، وتحقق مبني على الأدلة. هذه ليست حزمة قوالب، وليست مجموعة سكربتات: الملفات هنا
تعليمات وتصميم قرارات، والوكيل يكتب كل الكود الذي يحتاجه المشروع بنفسه.

**An instruction-only Agent Skill for original Arabic-first motion design.** No bundled scripts, no
runtimes, no demo film, no catalogs to parse — just the production playbook an agent needs, loaded
progressively.

> v3.0.0 — full restructure: 400+ files of tooling and vendor code replaced by one skill, one hub
> file and 16 self-contained references. See [`docs/MIGRATION-v3.md`](docs/MIGRATION-v3.md).

---

## ما يوجد هنا / What's here

```
skills/arabic-motion-forge/
  SKILL.md                 ← نقطة الدخول الوحيدة / the single entry point (~200 lines)
  references/              ← 16 ملفًا مستقلًا، تُقرأ عند الحاجة فقط / 16 on-demand files
  assets/
    templates/             ← brief · spec · style · layer-plan · journal
    fonts/                 ← IBM Plex Sans Arabic (Regular + SemiBold, OFL)
docs/
  ARCHITECTURE.md          ← لماذا هذا الشكل، وميزانية السياق / why this shape
  MIGRATION-v3.md          ← خريطة ما حُذف وما حلّ محله / what was removed and why
AGENTS.md · CLAUDE.md      ← مداخل رقيقة لأدوات أخرى / thin entry points for other agents
.claude-plugin/            ← تثبيت كمكوّن Claude Code / plugin manifests
```

References: `workflow` · `spec-and-reframe` · `creative-direction` · `story-structure` ·
`motion-craft` · `motion-mechanics-text` · `motion-mechanics-physics` · `motion-mechanics-space` ·
`easing-and-timing` · `arabic-typography` · `audio-and-sync` · `assets-and-provenance` ·
`implementation` · `quality-gates` · `platform-delivery` · `after-effects-equivalence`.

## التثبيت / Install

**Claude Code — as a personal skill** (any project, no workspace pollution):

```bash
git clone https://github.com/zoih6/arabic-motion-forge.git
cp -r arabic-motion-forge/skills/arabic-motion-forge ~/.claude/skills/
```

**Claude Code — as a plugin / marketplace** (team install, keeps the repo updatable):

```bash
/plugin marketplace add zoih6/arabic-motion-forge
/plugin install arabic-motion-forge
```

**أي وكيل آخر / Any other agent (Codex, Cursor, Gemini, custom):** point it at
`skills/arabic-motion-forge/SKILL.md` (or just `AGENTS.md`, which forwards to it). Skills are plain
Markdown — nothing to build, nothing to run.

**Test**: ask for `إعلان 30 ثانية لمطعم` or "a 20-second kinetic-typography reel in Arabic". The
agent must greet you, ask the five intake questions, then work.

## كيف يعمل / How it works

Skill conventions, applied strictly:

1. **Level 1 — metadata**: only `name` + `description` sit in context at startup (~100 tokens).
2. **Level 2 — `SKILL.md`**: loaded when the skill matches; it holds the laws, the route table and
   the phase gates. Under 500 lines, on purpose.
3. **Level 3 — `references/`**: opened only when a task needs them, always one hop from `SKILL.md`
   (never a reference pointing at another reference), each self-contained.

The agent therefore carries the whole system without paying for it until it is needed — and writes
its own renderer, encoder calls and QA scripts for the project at hand, where they belong.

## ما يضمنه النظام / What the skill enforces

- **Arabic correctness**: shaping-first animation (words/lines as units), font readiness before frame
  one, measured metrics, bidi isolation, lam-alef/final checks, copy hash lock.
- **Motion quality**: layer decomposition, three-stage timing, engineered reveals, motivated camera,
  relation-based transitions, and an objective anti-slideshow audit instead of taste-by-vibes.
- **Originality**: three structurally distinct directions on eight axes, a signature move, a written
  refusal, and a rule that forbids reusing a palette, composition or scene code from elsewhere.
- **Determinism**: `seek(t)` purity, no randomness, checkpointed rendering, PTS/frame/decode
  verification.
- **Honesty**: equivalence labels for every "After Effects-like" technique, measured loudness, and an
  explicit list of what the agent could not verify (it cannot hear, it cannot test on your phone).

## ما لا يوجد هنا (بقرار) / Deliberately absent

No `scripts/`, `runtime/`, `tools/`, `tests/`, `vendor/`, `catalog/` JSON, demo compositions or
research dumps. They consumed context on every session and encoded decisions the agent should make.
Third-party engines stay where they belong: installed per project, with their licences checked at
that moment (`references/implementation.md` §1, §8).

## الترخيص / License

MIT for this skill's own text and templates. Bundled font: IBM Plex Sans Arabic, SIL OFL 1.1 —
see [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md).
