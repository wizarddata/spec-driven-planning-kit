# spec-driven-planning-kit

Portable, tool-agnostic kit for solo + AI planning. Two stages: scoping (problem → `SCOPE.md`) and implementation planning (`PLAN.md` + `PROGRESS.md`).

## Contents

| File | Purpose |
|---|---|
| [`scoping-rules.md`](./scoping-rules.md) | Stage 1: problem framing, option surfacing, `SCOPE.md` output. |
| [`spec-driven-planning-kit.md`](./spec-driven-planning-kit.md) | Stage 2: sweep-vs-bend lock loop, `PLAN.md` + `PROGRESS.md` templates, phase close + extract ritual, commit conventions. |


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

Agent reads §1..§7, runs sweep-vs-bend lock loop, writes `PLAN.md` (canonical spec) AND seeds `PROGRESS.md` with the Phase 1 working slice. Kit conventions header is copied into both files so context-clear resumes get conventions regardless of which file loads.

### Resuming an implementation in flight after context clear

```
read <path>/PROGRESS.md, resume
```

Only `PROGRESS.md` loads on resume. `PLAN.md` stays cold during a phase; it is touched only at phase boundaries (close + extract next phase slice). Per-resume token cost stays bounded to the active phase's working slice.

## Tool agnosticism

Substitute your agent's instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, `AGENTS.md`) wherever the kit says `AGENTS.md`.

## Project folder convention

```
<TARGET>_<PURPOSE>_<INTERFACE>/
├── SCOPE.md            # Stage 1 output
├── PLAN.md             # Stage 2 output (per implementation)
├── PROGRESS.md         # active-phase working slice (only file read on resume)
└── README.md           # minimal pointer
```

## Gotchas

- **Scoping must finish in one session.** Scoping rules don't have a resume model. Mid-scope context clear loses Rule-5 lock state. Complete `SCOPE.md` before /clear.
- **Custom kit modifications need both files.** Kit conventions header lives in BOTH `PLAN.md` and `PROGRESS.md`. Edit the header in one place only → drift on next phase resume. Sync both.
- **Phase close in single session.** Two-commit ritual (sync + extract) must complete without /clear between them. Mid-ritual context clear would lose PROGRESS deltas before they sync to PLAN.
- **Fresh-session auto-population risk.** Agent on a fresh context may attempt to populate the rest of the implementation plan automatically without user input. Resume command (`read <path>/PROGRESS.md, resume`) constrains scope but isn't bulletproof. Watch for unprompted PLAN writes mid-phase.