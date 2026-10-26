# AGENTS.md — entry point for any coding agent

This repository is an **Agent Skill**, not an application. There is nothing to install, build or run.

**Read [`skills/arabic-motion-forge/SKILL.md`](skills/arabic-motion-forge/SKILL.md) and follow it.**
Everything else in this repo is either a reference that `SKILL.md` points to on demand, or
documentation for humans (`README.md`, `docs/`).

Rules for agents working *with* the skill:

1. Do not copy skill files into the user's project. Read them from this repo (or the installed skill
   directory) and act on them.
2. Load only the references the current task needs. One hop: `SKILL.md` → reference file.
3. The skill contains no executables by design. Write the code each project needs yourself, inside
   the user's project folder, and keep it reproducible.
4. Never present a technique as a native Adobe/tool feature. Report equivalences honestly
   (`skills/arabic-motion-forge/references/after-effects-equivalence.md`).

Rules for agents editing *this* repo: see [`CLAUDE.md`](CLAUDE.md).
