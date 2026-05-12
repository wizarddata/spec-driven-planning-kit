# Spec-Driven Planning Kit

Agent reads file, follows §1..§7 when planning implementation.

**Tool agnosticism:** substitute your tool's agent-instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, etc.) for `AGENTS.md`.

---

## §1. Kickoff

User invokes: `"use kit at <path> to plan <implementation>"`. Agent reads kit, follows §1..§7. Kickoff writes PLAN.md (canonical spec) + PROGRESS.md (Phase 1 slice extracted from PLAN).

Implementation in flight: no re-kickoff. **Resume reads PROGRESS.md only.** PLAN.md read-only during phase; loads at phase boundaries (close + extract next slice). Resume command: `read <path>/PROGRESS.md, resume`.

`## Kit conventions` header (§4) copied verbatim into BOTH PLAN.md and PROGRESS.md. Conventions survive context clears regardless of which file loads.

## §2. Sweep-vs-bend

Every locked decision picks verdict — `bend`, `judge`, `sweep` — via cost/benefit matrix below. Forces explicit compare of compromise vs clean-slate (greenfield) alternative. Prevents silent accept of bent decisions when refactor cheaper than long-term debt.

**Step 1 — bucket sweep cost** (blast radius of greenfield version):
- **S** — ≤50 LOC, single subsystem, no migration.
- **M** — 50-200 LOC OR multi-file refactor, no wire/data shape change.
- **L** — >200 LOC OR wire/data shape change OR breaks Trap-rated invariant.

**Step 2 — bucket sweep benefit** (gain from greenfield):
- **S** — cosmetic / naming / placement.
- **M** — architectural cleanup; removes one bent constraint.
- **L** — unblocks future work; removes chain of bends; converts trap to non-issue.

**Step 3 — look up verdict:**

```
            BENEFIT
            S      M       L
COST  S    bend  sweep  sweep
COST  M    bend  judge  sweep
COST  L    bend  bend   judge
```

**Step 4 — apply verdict:**
- `bend` → record locked decision; continue planning.
- `judge` → output cost+benefit summary; await explicit user lock; do not lock alone.
- `sweep` → halt planning; output sweep alternative; do not lock until user confirms (continue with sweep, or override to bend).

**Locked-decision row format** (PROGRESS.md "Locked decisions" tables):

```
| # | Decision | Sweep cost | Sweep benefit | Verdict | Notes |
```

Notes required on every row. One line: greenfield version + concrete blocking cost (LOC count, file count, trap reference, dependency name).

**Uncertainty tag:** cost or benefit was default-M (agent uncertain on bucket) → append `(default-M)` to Notes. User reviews post-lock, can override bucket.

**User-suggestion mapping rule:** user proposes solution mid-planning (not Y/N answer) → agent buckets suggestion's cost+benefit per matrix BEFORE evaluating; outputs verdict (`bend` / `judge` / `sweep`) alongside response.

---

## §3. Stuck-prompts

Agent fires autonomously on hit. User can fire manually.

- Cost or benefit not bucketed → *"Pick S/M/L for each. Anchor to LOC count, file count, or trap reference."*
- Verdict = `sweep`, agent tried to lock bend → *"Verdict sweep. Halt planning. Output sweep alternative to user."*
- Verdict = `judge` → *"Judgment call. Output cost+benefit to user. Do not lock alone."*

---

## §4. PLAN.md template

```markdown
# <implementation> — Plan

> **Decision rationale:** `git log -- PLAN.md`.

```yaml
status: planning | active | done
revised: YYYY-MM-DD
```

## Kit conventions (do not delete — survives context clears, do not deduplicate)

> Header copied verbatim into every PLAN.md and PROGRESS.md.

- **Sweep-vs-bend** (every locked decision):
  - **Cost buckets** (sweep blast radius): S ≤50 LOC, no migration. M 50-200 LOC OR multi-file refactor, no wire/data shape change. L >200 LOC OR wire/data shape change OR breaks Trap-rated invariant.
  - **Benefit buckets** (greenfield gain): S cosmetic / naming / placement. M architectural cleanup, removes one bent constraint. L unblocks future work, removes chain of bends, converts trap to non-issue.
  - **Verdict matrix:**
    ```
                BENEFIT
                S      M       L
    COST  S    bend  sweep  sweep
    COST  M    bend  judge  sweep
    COST  L    bend  bend   judge
    ```
  - **Verdict actions:** bend → record + continue. judge → surface cost+benefit, await user lock. sweep → halt planning, surface alternative.
  - **Lock-table row format:** `| # | Decision | Sweep cost | Sweep benefit | Verdict | Notes |`. Notes required, one line: greenfield version + concrete blocking cost. Append `(default-M)` to Notes when bucket was uncertainty default.
- **Doc style** (every kit-managed doc):
  - **WHAT, not WHY.** State current behavior + scope. Reasoning only in dedicated "Rationale" section if needed.
  - **Structure beats prose.** Tables for compares. Fenced code blocks. Lists for sets. Walls of text = restructure.
  - **Brief.** Cut filler, hedging, future-tense narration, "we"/"let's", trailing summaries.
  - **Caveman.** Drop articles + filler. Fragments OK. Technical terms exact. Code, commits, security warnings, irreversible-action confirmations stay normal.
- **User-suggestion mapping**: bucket cost + benefit per matrix BEFORE evaluating user proposal. Output verdict.
- **Plan-edit commits**:
    - `plan: <area> — <change>` (PLAN edits — only at kickoff or phase boundary)
    - `plan: phase <N> sync` (close commit 1 — PLAN.md only: merge PROGRESS deltas + mark shipped)
    - `plan: extract phase <N+1>` (close commit 2 — PROGRESS.md only: overwrite w/ next phase slice)
    - Phase close runs both commits in same session. Do NOT /clear mid-ritual.
- **Stuck-prompts** (agent fires autonomously):
    - Cost or benefit not bucketed → *"Pick S/M/L. Default M if unsure. Anchor to LOC count, file count, or trap reference."*
    - Verdict = sweep, attempted bend lock → *"Verdict sweep. Halt planning. Output sweep alternative."*
    - Verdict = judge → *"Judgment call. Output cost+benefit. Do not lock alone."*
- **File roles**: PLAN.md = canonical spec, read-only during phase. PROGRESS.md = active-phase working slice, ONLY file read on resume. Mid-phase edits land in PROGRESS only.
- **Phase close** (user-fired): sync PROGRESS deltas → PLAN; mark phase shipped; extract next phase slice → overwrite PROGRESS. PLAN write only at phase boundary. Mid-impl surprises route immediately to PROGRESS active risks / AGENTS trap / code comment / PROGRESS open question / discard — never batched. No catch-all "phase close notes" or "mid-impl decisions" section.

## §1. Closed enums

Every union type, exhaustively listed. Anti-hallucination guardrail.

## §2. Schema lock

Canonical data shape lives as typed source in repo (path varies by language: `schema/types.ts`, `schema/*.proto`, `pkg/schema/*.go`, etc.). PLAN.md keeps 1-line pointer:

```
schema-lock: <repo-path>
```

Project's type-checker / build system enforces conformance. Discriminated unions explicit in source. Drift between PLAN and code impossible — code IS spec.

## §3-N. Subsystems

One section per concern.

## Phase plan

```yaml
phase_1: { ships: [...], demo: "<unambiguous done check>" }
phase_2: ...
```

## File scope

Paths only. No per-file purpose comments. Agent re-derives purpose by reading file on demand.

```yaml
new:      [...]
modified: [...]
deleted:  [...]
```

## Test additions

Test points proving each phase landed.

## Risk register

Active risks only. Resolved risks discarded.
```

---

## §5. PROGRESS.md template (active-phase working slice)

PROGRESS holds everything needed to resume work on active phase WITHOUT loading PLAN.md. Slice extracted from PLAN.md at phase boundary (§6). Carries: kit conventions, active phase lock table, file scope, test additions, active risks (incl. multi-phase carry-forwards), open questions, schema-lock pointer.

```markdown
# <implementation> — Progress

## Kit conventions

> Copied verbatim from PLAN.md §4 conventions header. Survives context clears.

<full conventions header — same content as PLAN.md>

## Phase N — <name>

### Lock table

| # | Decision | Sweep cost | Sweep benefit | Verdict | Notes |
|---|----------|-----------|--------------|---------|-------|
| N1 | ... | ... | ... | ... | ... |

### File scope

```yaml
new:      [...]
modified: [...]
deleted:  [...]
```

### Test additions

- ...

### Active risks (this phase + carry-forwards)

- R<n> — ...

### Open questions blocking this phase

- ...

### Boundary metadata

- `schema-lock: <repo-path>`
- `plan-source: PLAN.md §<area>`
```

Mid-phase edits land in PROGRESS only. PLAN.md read-only during phase. New risks, new open questions, scope amendments all touch PROGRESS. At phase close, deltas sync back to PLAN (§6).

---

## §6. Phase close + mid-impl routing

**Phase close** (user-fired). Two-commit ritual in single session. Do NOT /clear mid-ritual — mid-ritual context clear loses sync state.

**Commit 1 — sync** (PLAN.md only):

1. **Sync PROGRESS deltas → PLAN.md.** Merge mid-phase additions back into PLAN:
   - New risks in PROGRESS active-risks list → append to PLAN risk register (tag active for downstream phases if multi-phase).
   - Resolved risks → delete from PLAN.
   - New open questions → PLAN open questions section.
   - Lock-table amendments → update PLAN active-phase section.
2. **Mark phase shipped** in PLAN.md phase plan (`phase_N: { shipped: true, demo: "..." }`).
3. Commit: `plan: phase <N> sync`.

**Commit 2 — extract** (PROGRESS.md only):

1. **Overwrite PROGRESS.md with next phase slice from PLAN.md:**
   - Kit conventions header (verbatim copy from PLAN §4).
   - Next phase's lock table, file scope, test additions.
   - Carry-forward active risks (PLAN risks tagged for phase N+1).
   - Open questions blocking phase N+1.
   - Boundary metadata (schema-lock path, plan-source pointer).
2. Commit: `plan: extract phase <N+1>`.

Bad extract → revert commit 2 without losing commit 1's sync.

After commit 2, resume from PROGRESS picks up at phase N+1 without re-loading PLAN.

**Mid-impl surprise routing** (continuous, never batched). Every deviation routes IMMEDIATELY to exactly one of:

- **PROGRESS active risks** — phase-bounded risk emerging during this phase.
- **AGENTS.md trap entry** — cross-phase pattern future code must reuse or avoid.
- **Code comment at site** — single-site implementation detail.
- **PROGRESS open questions** — TODO requiring user decision.
- **Discard** — one-shot historical fact, no audit value.

PLAN.md **read-only during phase**. Sync ritual at phase close is only write opportunity. No "phase close notes" or "mid-impl decisions" catch-all sections in either file.

---

## §7. Git commit conventions for plan changes

PLAN.md changes commit separately from code (one logical change per commit).

**Decision / plan edit:**
```
plan: <area> — <change>
```

**Phase close** (two commits in same session — see §6):
```
plan: phase <N> sync           (PLAN.md only: merge deltas + mark shipped)
plan: extract phase <N+1>      (PROGRESS.md only: overwrite with next phase slice)
```

Two-commit pattern gives revert granularity. Bad extract reverts without losing sync. Body optional — file diff carries meaningful content.
