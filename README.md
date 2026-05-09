# spec-driven-planning-kit

Single-document portable kit for solo + AI planning of features and full projects.

## Contents

- [`spec-driven-planning-kit.md`](./spec-driven-planning-kit.md) — the kit itself

## Install

One time per computer.

1. Pull the kit anywhere on disk:

   ```sh
   git clone https://github.com/wizarddata/spec-driven-planning-kit ~/Documents/spec-driven-planning-kit
   ```

2. First time you want to use the kit in any project, tell your agent:

   > use the kit at ~/Documents/spec-driven-planning-kit/spec-driven-planning-kit.md

3. Agent reads the kit, runs the §0 install bootstrap, and prompts:

   > Spec-driven planning kit not installed in your global agent-instruction file. Install §9 snippet now (one-time per machine, 2 lines)? [y/N]

4. Answer `y`. Agent appends 2 lines to your global agent-instruction file (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`, `~/.cursor/.cursorrules`, etc.) — a marker + the kit path.

5. Done forever. Future sessions auto-load the 2-line pointer; agent re-reads the kit file only when you fire a kickoff phrase. Operational rules live in PLAN.md / PROGRESS.md (kit-generated, with conventions baked into every file's header) so the agent always has them in context for any feature already in flight without bloating CLAUDE.md.

## Use

Until you fire a kickoff phrase, the agent treats requests normally — no kit overhead on simple fixes.

Kickoff phrases:
- `/kit <feature>`
- `"let's plan this with the kit"`
- `"use the kit"`
- `"engage the kit"`

After kickoff (and one-time install if needed), the agent runs the full lock-iteration loop, writes `PLAN.md` (with the "Kit conventions" header section auto-included), and (for multi-session features) maintains `PROGRESS.md`. Conventions live in those files; agent reads them on every resume.

## Update

Future kit revisions bump the marker (`v1` → `v2`). On next kickoff after a kit revision, agent detects stale marker and prompts to upgrade — re-pastes new pointer, replaces old block. Re-running install is always safe (idempotent).

## Why the snippet is just 2 lines

CLAUDE.md is shared by every session in every project on the machine — bloating it is expensive. The kit splits responsibilities:

| Lives in | Cost | Content |
|---|---|---|
| `~/.claude/CLAUDE.md` (global) | 2 lines × every session forever | Marker + kit path + kickoff phrases. Pure discovery pointer. |
| Kit file (`spec-driven-planning-kit.md`) | Read once on kickoff | Full mechanics reference. |
| `PLAN.md` "Kit conventions" header | Auto-loaded with PLAN.md per feature | Operational rules (recommendation format, commit format, promote-and-reset, density signals) — kit bakes them in. |

Agent reads PLAN.md whenever you resume a feature, so the conventions are always in context for any active feature without paying CLAUDE.md cost.

## Philosophy

Format alone is ~25% of why this kit produces good outcomes. The rest is substrate — fast tests, project-specific lint, agent-instruction file with invariants, structured commit messages, user discipline. See §11 (substrate prereqs) and §14 (counterfactuals).
