# spec-driven-planning-kit

Portable, tool-agnostic kit for solo + AI planning. Two stages: scoping (problem → `SCOPE.md`) and implementation planning (`PLAN.md` + `PROGRESS.md` + per-phase docs + `RISKS.md`).

## Contents

```yaml
- file: scoping-rules.md
  purpose: "Stage 1 — problem framing, option surfacing, SCOPE.md output."
- file: spec-driven-planning-kit.md
  purpose: "Stage 2 — sweep-vs-bend lock loop, PLAN + PROGRESS + phase docs + RISKS templates, phase-close ritual, commit conventions."
```

## Use

### Stage 1 — Scoping a new project

```
use the scoping rules at <path>/scoping-rules.md to scope <project>
```

Agent runs Rules 1-7, brainstorms feature-gap pass, writes `SCOPE.md`.

### Stage 2 — Implementation planning

```
use the kit at <path>/spec-driven-planning-kit.md to plan <implementation>
```

Agent reads §0..§6, applies arch_decisions rule on each lock, writes:

```yaml
- PLAN.md            # canonical arch spec + Kit conventions header
- PROGRESS.md        # active-phase pointer + state-of-branch
- phases/phase1-*.md # per-phase scope + sub-tasks + locks (one file per phase)
- RISKS.md           # append-only risk register
```

### Resuming an implementation in flight after context clear

```
read <path>/PLAN.md and PROGRESS.md, resume
```

PLAN loads conventions; PROGRESS points to active phase doc; conventions tell agent to also read that phase doc + `RISKS.md (status:active)`. Closed phase docs (`phases/closed/*`) are audit, not read on resume.

### Phase close

Triggered by user reply "close" to the §3 phase-completion nag, or fired manually with "close phase <N>". Agent runs the §6 ritual as a TaskList — one task per step, tick each, single commit at end:

```yaml
- verify all sub-tasks done
- verify demo commit exists
- update PLAN phase index → status:shipped
- move phase doc → phases/closed/
- reset PROGRESS pointer to next phase
- promote load-bearing notes → RISKS or AGENTS.md
- commit "plan: phase <N> close — archive"
```

Skipped step = unticked task = visible to user.

## Tool agnosticism

Substitute your agent's instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, `AGENTS.md`) wherever the kit says `AGENTS.md`.

## Format rule

Pipe tables banned in kit-managed docs. All structured data in fenced YAML. Fence keeps the syntax from bleeding into agent's unrelated output (commits, prose, replies). See `spec-driven-planning-kit.md` §0.

## Project folder convention

```
<TARGET>_<PURPOSE>_<INTERFACE>/
├── SCOPE.md              # Stage 1 output
├── PLAN.md               # Stage 2 arch spec + Kit conventions
├── PROGRESS.md           # active-phase pointer + state-of-branch
├── RISKS.md              # append-only risk register
├── phases/
│   ├── phase1-<name>.md  # active or planning phases
│   └── phase2-<name>.md
├── phases/closed/
│   └── phase0-<name>.md  # shipped phases, frozen audit
└── README.md             # minimal pointer
```
