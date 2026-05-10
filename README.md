# spec-driven-planning-kit

Portable, tool-agnostic kit for solo + AI planning. Two stages: scoping (problem → `SCOPE.md`) and implementation planning (`PLAN.md` + `PROGRESS.md`).

## Contents

| File | Purpose |
|---|---|
| [`scoping-rules.md`](./scoping-rules.md) | Stage 1: problem framing, option surfacing, `SCOPE.md` output. |
| [`spec-driven-planning-kit.md`](./spec-driven-planning-kit.md) | Stage 2: sweep-vs-bend lock loop, `PLAN.md` + `PROGRESS.md` templates, promote-and-reset, commit conventions. |


## Use

### Stage 1 — Scoping a new project

```
use the scoping rules at <path>/scoping-rules.md to scope <project>
```

Agent runs Rules 1-7, brainstorms feature-gap pass, writes `SCOPE.md`

### Stage 2 — Implementation planning

```
use the kit at <path>/spec-driven-planning-kit.md to plan <implementation>
```

Agent reads §1..§7, runs sweep-vs-bend lock loop, writes `PLAN.md` (with `## Kit conventions` header auto-included). For multi-session implementations, also maintains `PROGRESS.md`.

### Resuming an implementation in flight after context clear

```
read <path>/PLAN.md and PROGRESS.md, resume
```

No re-kickoff. The `## Kit conventions` header lives inside `PLAN.md`, so conventions reload on every resume.

## Tool agnosticism

Substitute your agent's instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, `AGENTS.md`) wherever the kit says `AGENTS.md`.

## Project folder convention

```
<TARGET>_<PURPOSE>_<INTERFACE>/
├── SCOPE.md            # Stage 1 output
├── PLAN.md             # Stage 2 output (per implementation)
├── PROGRESS.md         # active-phase scratchpad
└── README.md           # minimal pointer
```
