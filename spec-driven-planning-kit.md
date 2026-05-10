# Spec-Driven Planning Kit

Agent reads this file and follows §1..§7 when planning an implementation.

**Tool agnosticism:** Substitute your tool's agent-instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, etc.) for `AGENTS.md`.

---

## §1. Kickoff

User invokes via `"use the kit at <path> to plan <implementation>"`. Agent reads kit and follows §1..§7.

For an implementation in flight (PLAN.md present): no re-kickoff. Agent reads PLAN; the `## Kit conventions` header (§4) is part of every PLAN. Resume command "read .../*IMPLEMENTATION NAME*/PLAN.md and PROGRESS.md, resume Phase 3".

## §2. Sweep-vs-bend

Every locked decision picks one of three verdicts — `bend`, `judge`, `sweep` — by bucketing cost and benefit on the matrix below. Forces explicit comparison of the chosen compromise against a clean-slate (greenfield) alternative; prevents the agent from silently accepting bent decisions when a refactor would be cheaper than the long-term debt.

**Step 1 — bucket the sweep cost (blast radius of doing the greenfield version):**
- **S** — ≤50 LOC, single subsystem, no migration.
- **M** — 50-200 LOC OR multi-file refactor with no wire/data shape change.
- **L** — >200 LOC OR wire/data shape change OR breaks a Trap-rated invariant.

**Step 2 — bucket the sweep benefit (gain from the greenfield version):**
- **S** — cosmetic / naming / placement only.
- **M** — architectural cleanup; removes one bent constraint.
- **L** — unblocks future work; removes a chain of bends; converts a trap into a non-issue.

**Step 3 — look up the verdict:**

```
            BENEFIT
            S      M       L
COST  S    bend  sweep  sweep
COST  M    bend  judge  sweep
COST  L    bend  bend   judge
```

**Step 4 — apply the verdict:**
- `bend` → record locked decision; continue planning.
- `judge` → output cost+benefit summary to user; await explicit lock; do not lock alone.
- `sweep` → halt planning; output sweep alternative to user; do not lock until user confirms (continue with sweep, or override to bend).

**Locked-decision row format** (used in PROGRESS.md "Locked decisions" tables):

```
| # | Decision | Sweep cost | Sweep benefit | Verdict | Notes |
```

Notes field required on every row. One line: greenfield version + concrete blocking cost (LOC count, file count, trap reference, dependency name).

**Uncertainty tag:** when cost or benefit was a default-M (AI uncertain on bucket), append `(default-M)` to Notes. User reviews post-lock and can override the bucket.

**User-suggestion mapping rule:** when the user proposes a solution mid-planning (not just answering Y/N), the agent buckets the suggestion's cost + benefit per the matrix BEFORE evaluating, and outputs the verdict (`bend` / `judge` / `sweep`) alongside the response.

---

## §3. Stuck-prompts

Agent fires autonomously when conditions hit. User can fire manually.

- Cost or benefit not bucketed → ask: *"Pick S/M/L for each. Anchor to LOC count, file count, or trap reference."*
- Verdict = `sweep`, agent attempted to lock the bend anyway → ask: *"Verdict sweep. Halt planning. Output sweep alternative to user."*
- Verdict = `judge` → ask: *"Judgment call. Output cost+benefit to user. Do not lock alone."*

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

> Header copied verbatim into every PLAN.md.

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
  - **Lock-table row format:** `| # | Decision | Sweep cost | Sweep benefit | Verdict | Notes |`. Notes required, one line: greenfield version + concrete blocking cost. Append `(default-M)` to Notes when bucket was an uncertainty default.
- **Doc style** (every kit-managed doc):
  - **WHAT, not WHY.** State current behavior + scope. Reasoning only in dedicated "Rationale" section if needed.
  - **Structure beats prose.** Tables for compares. Fenced code blocks. Lists for sets. Walls of text = restructure.
  - **Brief.** Cut filler, hedging, future-tense narration, "we"/"let's", trailing summaries.
- **User-suggestion mapping**: bucket cost + benefit per matrix BEFORE evaluating user proposal. Output verdict.
- **Plan-edit commits**:
    - `plan: <area> — <change>`
    - `plan: phase <N> close — promote-and-reset`
- **Stuck-prompts** (agent fires autonomously):
    - Cost or benefit not bucketed → *"Pick S/M/L. Default M if unsure. Anchor to LOC count, file count, or trap reference."*
    - Verdict = sweep, attempted bend lock → *"Verdict sweep. Halt planning. Output sweep alternative."*
    - Verdict = judge → *"Judgment call. Output cost+benefit. Do not lock alone."*
- **Promote-and-reset** (user-fired at phase close): migrate mid-impl decisions/surprises → PLAN risks or AGENTS traps; discard sub-tasks + commit log; collapse state-of-branch → 1-line phase plan entry; reset PROGRESS to next phase.

## §1. Glossary

Named concepts, one line each. Domain vocabulary the spec uses without re-defining.

## §2. Closed enums

Every union type, exhaustively listed. Anti-hallucination guardrail.

## §3. Schema

Canonical data shape. JSONC with comments OK. Discriminated unions explicit.

## §4-N. Subsystems

One section per concern.

## Phase plan

```yaml
phase_1: { ships: [...], demo: "<unambiguous done check>" }
phase_2: ...
```

## File scope

```yaml
new:      [...]
modified: [...]
deleted:  [...]
```

## Test additions

Test points that prove each phase landed.

## Risk register

Active risks only. Resolved risks discarded or strikethrough.
```

---

## §5. PROGRESS.md template (active phase only)

```markdown
# <implementation> — Progress

> Conventions live in PLAN.md "Kit conventions" header. Do not duplicate.

## Phase 1 — <name>

### Sub-tasks
- [ ] T1 — <thing>
- [ ] T2 — ...

### Commit log
<hash> phase1: <area> — <change>

### Mid-impl decisions / surprises
- <deviation> — <reason>

### State-of-branch checkpoint
<one paragraph after major commits — most recent only, prior overwritten>
```

PROGRESS.md never accumulates. User fires promote-and-reset at phase close (§6).

---

## §6. Promote-and-reset

User-fired at phase close. Agent runs:

1. **Mid-impl decisions / surprises** → migrate to PLAN.md risk register OR AGENTS.md trap entries (whichever fits).
2. **Sub-task checklist** → discard.
3. **Commit log block** → discard.
4. **State-of-branch checkpoint** → collapse to 1-line phase plan entry in PLAN.md (`Phase 2 shipped: <demo>`).
5. **PROGRESS.md** → reset to next phase template (header + empty sub-tasks).

Phase rollover commits with format from §7:
```
plan: phase 2 close — promote-and-reset
```

---

## §7. Git commit conventions for plan changes

PLAN.md changes commit separately from code (one logical change per commit).

**Decision / plan edit:**
```
plan: <area> — <change>
```

**Phase close:**
```
plan: phase <N> close — promote-and-reset

Promoted to PLAN.md risk register:
- <decision> — <why load-bearing>
Promoted to AGENTS.md trap entries:
- <trap> — <why future readers need it>
Discarded:
- Sub-task checklist
- Commit log
- State-of-branch (collapsed to phase plan one-liner)
```

