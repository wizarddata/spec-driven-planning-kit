# spec-driven-planning-kit

Single-document portable kit for solo + AI planning of features and full projects.

## Contents

- [`spec-driven-planning-kit.md`](./spec-driven-planning-kit.md) — the kit itself

## Install

1. Copy `spec-driven-planning-kit.md` into your project root (or keep one global copy)
2. Paste the §9 installation snippet into your project's `AGENTS.md` (or `CLAUDE.md` / `.cursorrules` / equivalent)
3. Done

## Use

Until you invoke the kit explicitly, the agent treats requests normally — no documentation overhead on simple fixes.

Kickoff phrases:
- `/kit <feature>`
- `"let's plan this with the kit"`
- `"use the kit"`
- `"engage the kit"`

After kickoff, the agent runs the full lock-iteration loop, writes `PLAN.md`, and (for multi-session features) maintains `PROGRESS.md`. See the kit for details.

## Philosophy

Format alone is ~25% of why this kit produces good outcomes. The rest is substrate — fast tests, project-specific lint, agent-instruction file with invariants, structured commit messages, user discipline. See §11 (substrate prereqs) and §14 (counterfactuals).
