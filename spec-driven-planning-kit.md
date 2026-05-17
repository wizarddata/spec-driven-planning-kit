# Spec-Driven Planning Kit

Agent reads §0..§6. Tool agnosticism: substitute your agent-instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, etc.) for `AGENTS.md`.

---

## §0. Format

Pipe tables banned. All structured data in fenced YAML. `|` for multi-line.

## §1. Kickoff & resume

```yaml
kickoff: "use the kit at <path>/spec-driven-planning-kit.md to plan <impl>"
resume:  "read <path>/PLAN.md and PROGRESS.md, resume"
closed:  "phases/closed/* = audit, not read on resume"
```

## §2. Arch decisions

```yaml
rule: |
  Before locking any decision that bends existing arch, internally weigh
  the rewrite alternative. Lock the local fix only if rewrite is concretely
  more expensive. Surface both options to user when genuinely unsure.

lock_row:
  - n: 1
    decision: <short title>
    constraint: <tag>     # what's being bent around — enables chain detector
    note: <one line>

chain_detector: |
  Before locking a bend, grep prior locks for same constraint tag.
  Match → surface sweep candidate (may auto-resolve or escalate to user).
```

## §3. Phase-completion nag

```yaml
trigger: |
  After any commit agent judges likely completes the phase (last sub-task
  or matches demo description).
action: |
  Surface close prompt at end of reply — "Phase <N> ready to close: <X>/<Y>
  done, demo at <hash>. Reply 'close' to fire ritual, 'not yet' to defer."
suppress: |
  None. "not yet" drops for current reply only. Next phase-completing
  commit re-prompts. Re-firing is correct behavior — user may have forgotten.
```

## §4. PLAN.md template

````markdown
# <impl> — Plan

```yaml
status: planning | active | done
revised: YYYY-MM-DD
```

## Kit conventions (do not delete — survives /clear)

```yaml
- pipe_tables: banned; YAML fence for all structured data
- arch_decisions: |
    Weigh rewrite before bend. Lock local fix only if rewrite concretely
    costlier. Surface both options to user when unsure.
- lock_row: [n, decision, constraint, note]
- doc_style:
    - what_not_why (reasoning in Rationale section only)
    - structure beats prose (YAML/code for data; no walls)
    - brief (cut filler, hedging, future-tense, we/let's, trailing summaries)
- plan_sections:
    required: [§1 Glossary, §2 Closed enums, §3 Schema, §4-N Subsystems]
    rule: do not rename, do not drop numbering. Section absent → "N/A" line.
- file_roles:
    PLAN: arch spec, read-only during phase
    PROGRESS: active-phase pointer + state-of-branch
    phase_doc: phases/phase<N>-<name>.md — sole writable target during phase
    closed: phases/closed/ — frozen audit, not read on resume
    RISKS: append-only, status:active rows read on resume
- commits:
    plan_edit:     "plan: <area> — <change>"
    phase_open:    "plan: phase <N> open"
    phase_close:   "plan: phase <N> close — archive"
    code_in_phase: "phase<N>: <area> — <change>"
- phase_close_ritual:
    trigger: user replies "close" OR manual fire
    execution: agent creates TaskList from §6 steps, ticks each, single commit at end
    see: kit §6
```

## §1. Glossary
Domain terms used across PLAN. One line per term.

## §2. Closed enums
Locked sets — state machines, type variants, mode flags. Lock values here; do not invent new ones in code.

## §3. Schema
Data shapes — wire formats, DB schema, file formats, interface signatures.

## §4-N. Subsystems
One section per arch component. Naming + ordering up to project.

## Phase index

```yaml
- n: 1
  name: <slug>
  status: planning | active | shipped
  demo: "<unambiguous done check>"
  demo_commit: <hash>  # populated at close
  doc: phases/phase1-<name>.md
```

## File scope (project-wide)

```yaml
new: [...]
modified: [...]
deleted: [...]
```

## Architectural lock table
Format per kit §2. Phase-level locks live in phase doc.
````

## §5. PROGRESS.md template

PROGRESS = YAML block (`active_phase`, `phase_doc`, `status`, `revised`) at top of file + `## State-of-branch` paragraph citing latest commit hash inline as resume anchor.

## §5b. Phase doc template — `phases/phase<N>-<name>.md`

````markdown
# Phase <N> — <name>

```yaml
status: planning | active | shipped
opened: YYYY-MM-DD
closed: YYYY-MM-DD     # populated at archive
demo: "<unambiguous done check>"
demo_commit: <hash>    # populated at archive
reads: [PLAN.md, plan/<subsystem>.md, ...]
```

## Scope

## Sub-tasks

```yaml
- { id: T1, done: false, what: ... }
```

## File scope (this phase)

```yaml
new: [...]
modified: [...]
deleted: [...]
```

## Phase locks
Format per kit §2.

## Phase notes
````

## §5c. RISKS.md template

````markdown
# <impl> — Risks

```yaml
- id: R1
  status: active | resolved | deferred
  source: "§<N> / P<N>"
  risk: |
    What can go wrong.
  mitigation: |
    What to do. On resolve, append: "Closed at <hash>. <fix>."
```
````

## §6. Phase-close ritual

Triggered by user replying "close" to the §3 phase-completion nag, OR manual fire ("close phase <N>").

Agent creates TaskList from these steps, ticks each as done, single commit at end:

```yaml
1. verify all sub-tasks done:true
2. verify state-of-branch references demo commit
3. update PLAN phase index → status:shipped + demo_commit
4. move phases/phase<N>-*.md → phases/closed/
5. in moved file: status:shipped, closed:<date>, demo_commit
6. reset PROGRESS pointer → next phase (or status:done)
7. promote load-bearing phase notes → RISKS or AGENTS.md
8. commit "plan: phase <N> close — archive"
```

Skipped step = unticked task = visible to user. No "all done" claim until every task ticked + commit landed.
