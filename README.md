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
├── PROGRESS.md         # active-phase scratchpad
└── README.md           # minimal pointer
```

## Gotchas (relevent to user only)

If context is cleared and implementation planning resumed, AI will consistently attempt to populate the rest of the implementation plan automatically, without user input, in fresh session. TODO: workaround needed

If decision making paradigms / lock formatting is modified from default behavior, will not persist if planning is resumed across multiple sessions.

for now: complete scope and planning each in single context for continuity

for very large project, consider breaking phase plan into sub-documents for each section, then referencing in PLAN.md, save on context per session. Scope is cheaper than kit, maybe iterate scope into sub-scopes?