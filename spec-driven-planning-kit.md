# Spec-Driven Planning Kit

Agent reads §0..§5. Tool agnosticism: substitute your agent-instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, etc.) for `AGENTS.md`.

---

## §0. Format

Pipe tables banned (syntax bleeds into commits / prose). Fenced YAML for structured data. `|` for multi-line.

## §1. Kickoff & resume

```yaml
kickoff: "use the kit at <path>/spec-driven-planning-kit.md to plan <impl>"
resume:  "read <path>/PLAN.md  and PROGRESS.md, resume"
closed:  "phases/closed/* = audit, not read on resume"
```

## §2. Sweep-vs-bend

Every locked decision picks `bend | judge | sweep` by bucketing sweep cost (greenfield blast radius) + benefit (greenfield gain).

```yaml
cost:
  S: "≤50 LOC, no migration"
  M: "50-200 LOC OR multi-file refactor, no wire/data shape change"
  L: ">200 LOC OR wire/data shape change OR breaks Trap-rated invariant"
benefit:
  S: "cosmetic / naming / placement"
  M: "architectural cleanup, removes one bent constraint"
  L: "unblocks future work, removes chain of bends, converts trap to non-issue"
matrix: |
              BENEFIT
              S      M       L
  COST  S    bend  sweep  sweep
  COST  M    bend  judge  sweep
  COST  L    bend  bend   judge
actions:
  bend:  "record + continue"
  judge: "surface cost+benefit, await user lock"
  sweep: "halt, surface alternative"
```

**Lock row format** (PLAN architectural locks + phase doc phase locks):

```yaml
- n: 1
  decision: <short title>
  sweep_cost: S | M | L
  sweep_benefit: S | M | L
  verdict: bend | judge | sweep
  notes: |
    Required. Greenfield version + concrete blocking cost (LOC, file count,
    trap ref, dep name). Append "(default-M)" inline when bucket was
    uncertainty default.
```

**User-suggestion mapping:** when user proposes a solution mid-planning (not Y/N), bucket cost+benefit BEFORE evaluating; output verdict alongside response.

## §3. Stuck-prompts

Agent fires autonomously when trigger hits. Phrasing flexible; trigger is load-bearing.

```yaml
- trigger: cost or benefit not bucketed
  action: ask user for S/M/L, anchor to LOC / file / trap ref

- trigger: verdict=sweep, agent attempted bend lock
  action: halt, surface sweep alternative

- trigger: verdict=judge
  action: surface cost+benefit, await user lock (do not lock alone)

- trigger: phase completion (nag-with-fast-confirm)
  fires_when: |
    all sub-tasks in phases/<active>.md are done:true AND
    git log --grep "^phase<N>:" shows a commit referencing the demo
  action: |
    surface "Phase <N> ready to close: <X>/<Y> done, demo at <hash>.
    Reply 'close' to fire ritual, 'not yet' to defer."
  fires_where: end of next reply, once per ready-transition
  suppress_when: user replies "not yet" / "skip close" / "stays open"
  on_close: run phase_close_ritual from PLAN conventions header
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
- format: pipe tables banned, fenced YAML for all structured data
- sweep_vs_bend: see kit §2 — bucket S/M/L cost+benefit, apply matrix
- user_suggestion_mapping: bucket BEFORE evaluating user proposal; output verdict
- doc_style:
    - what_not_why (reasoning in dedicated Rationale section only)
    - structure beats prose (YAML/code for data; no walls)
    - brief (cut filler, hedging, future-tense, we/let's, summaries)
- file_roles:
    PLAN: arch spec, read-only during phase
    PROGRESS: active-phase pointer + state-of-branch (resume anchor)
    phase_doc: phases/phase<N>-<name>.md — sole writable target during phase
    closed: phases/closed/ — frozen audit, not read on resume
    RISKS: append-only, status:active rows read on resume
- commits:
    plan_edit:     "plan: <area> — <change>"
    phase_open:    "plan: phase <N> open"
    phase_close:   "plan: phase <N> close — archive"
    code_in_phase: "phase<N>: <area> — <change>"
    filter:        'git log --grep "^phase<N>:"'
    no_doc_log:    "git log canonical; demo_commit lives in phase doc front matter"
- stuck_prompts: see kit §3 — bucket-missing, sweep-attempted-bend, judge, phase-completion-nag
- phase_close_ritual (trigger: user replies "close" OR manual fire):
    1. verify all sub-tasks done:true
    2. verify state-of-branch references demo commit
    3. update PLAN phase index → status:shipped + demo_commit
    4. move phases/phase<N>-*.md → phases/closed/
    5. in moved file: status:shipped, closed:<date>, demo_commit
    6. reset PROGRESS pointer → next phase (or status:done)
    7. promote load-bearing phase notes → RISKS or AGENTS.md
    8. commit "plan: phase <N> close — archive"
```

## §1. Glossary
## §2. Closed enums
## §3. Schema
## §4-N. Subsystems

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

PROGRESS = YAML front matter (`active_phase`, `phase_doc`, `status`, `revised`) + `## State-of-branch` paragraph citing latest commit hash inline as resume anchor.

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
