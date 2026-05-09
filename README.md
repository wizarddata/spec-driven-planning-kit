# spec-driven-planning-kit

Single-document portable kit for solo + AI planning of features and full projects.

## Contents

- [`spec-driven-planning-kit.md`](./spec-driven-planning-kit.md) — the kit itself

## Install

One time per computer. No manual paste.

1. Pull the kit anywhere on disk:

   ```sh
   git clone https://github.com/wizarddata/spec-driven-planning-kit ~/spec-driven-planning-kit
   ```

   (Or any path you like — agent only needs to be able to read it.)

2. First time you want to use the kit in any project, tell your agent:

   > use the kit at ~/spec-driven-planning-kit/spec-driven-planning-kit.md

3. Agent reads the kit, runs the §0 install bootstrap, and prompts:

   > Spec-driven planning kit not installed in your global agent-instruction file. Install §9 snippet now (one-time per machine, ~50 lines)? [y/N]

4. Answer `y`. Agent appends the snippet to your global agent-instruction file (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`, `~/.cursor/.cursorrules`, etc.) with marker `<!-- kit:installed:v1 -->`.

5. Done forever. Every future session in every project auto-loads the snippet via the global file. No re-paste, no per-project setup.

## Use

Until you fire a kickoff phrase, the agent treats requests normally — no kit overhead on simple fixes.

Kickoff phrases:
- `/kit <feature>`
- `"let's plan this with the kit"`
- `"use the kit"`
- `"engage the kit"`

After kickoff (and one-time install if needed), the agent runs the full lock-iteration loop, writes `PLAN.md`, and (for multi-session features) maintains `PROGRESS.md`. See the kit for details.

## Update

Future kit revisions bump the marker (`v1` → `v2`). On next kickoff after a kit revision, agent detects stale marker and prompts to upgrade — re-pastes new snippet, replaces old block. Re-running install is always safe (idempotent).

## Philosophy

Format alone is ~25% of why this kit produces good outcomes. The rest is substrate — fast tests, project-specific lint, agent-instruction file with invariants, structured commit messages, user discipline. See §11 (substrate prereqs) and §14 (counterfactuals).
