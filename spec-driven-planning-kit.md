# Spec-Driven Planning Kit

Agent reads this file and follows §1..§9 when planning a feature.

**Tool agnosticism:** Substitute your tool's agent-instruction filename (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`, etc.) for `AGENTS.md`.

---

## §1. Kickoff

User invokes via `"use the kit at <path> to plan <feature>"`. Agent reads kit and follows §1..§9.

For a feature in flight (PLAN.md present): no re-kickoff. Agent reads PLAN; the `## Kit conventions` header (§4) is part of every PLAN. Resume with `"continue"` or `"where were we"`.

---

## §2. Industry-standard citation

Every non-trivial choice during planning cites an industry-standard convention as basis. Tag-only citation: `"ADR (Nygard 2011)"`, `"12-factor §III"`, `"AGENTS.md spec (Codex/Aider/Claude)"`, `"JSON Schema + closed enums (k8s API ref)"`, or `"no direct convention; closest analog: X"`.

**User-suggestion mapping rule:** when user proposes a solution mid-planning (not just answering Y/N), agent identifies the matching industry pattern (or names "no match — novel territory") and cites the source BEFORE evaluating the proposal.

---

## §3. Stuck-prompts

Agent fires autonomously when conditions hit. User can fire manually.

- Stall (2+ rounds on a decision) or recommendation lacks convention basis → ask: *"How do popular [X] handle this? Name source. If novel, name closest analog and justify divergence."*
- Spec drafted but prose-heavy → ask: *"Prioritize AI compatibility formatting, human readability is not required."*

---

## §4. PLAN.md template

```markdown
# <feature> — Plan

> **Decision rationale:** `git log -- PLAN.md`.

```yaml
status: planning | active | done
revised: YYYY-MM-DD
```

## Kit conventions (do not delete)

> Header copied verbatim into every PLAN.md.

- **Citation**: every non-trivial choice cites an industry convention. Tag-only (e.g. `"ADR (Nygard 2011)"`, `"12-factor §III"`, `"no direct convention; closest analog: X"`).
- **User-suggestion mapping**: when user proposes mid-planning, agent identifies matching industry pattern (or "no match — novel territory") and cites source BEFORE evaluating.
- **Plan-edit commits**:
    - `plan: <area> — <change>`
    - `plan: phase <N> close — promote-and-reset`
- **Stuck-prompts** (agent fires autonomously):
    - Stall or weak convention basis → *"How do popular [X] handle this? Name source. If novel, name closest analog and justify divergence."*
    - Draft prose-heavy → *"Prioritize AI compatibility formatting, human readability is not required."*
- **Promote-and-reset (phase close)**: migrate mid-impl decisions/surprises → PLAN risks or AGENTS traps; discard sub-tasks + commit log; collapse state-of-branch → 1-line phase plan entry; reset PROGRESS to next phase.
- **Density signals**: same fact 3 ways → densify; reader scrolls past current state → restructure; closing-phase debris → promote-and-reset.

## §1. Glossary

5-15 named concepts, one line each. Domain vocabulary the spec uses without re-defining.

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
# <feature> — Progress

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

PROGRESS.md never accumulates. See §6.

---

## §6. Promote-and-reset

When phase closes, agent runs autonomously:

1. **Mid-impl decisions / surprises** → migrate to PLAN.md risk register OR AGENTS.md trap entries (whichever fits).
2. **Sub-task checklist** → discard.
3. **Commit log block** → discard.
4. **State-of-branch checkpoint** → collapse to 1-line phase plan entry in PLAN.md (`Phase 2 shipped: <demo>`).
5. **PROGRESS.md** → reset to next phase template (header + empty sub-tasks).

Phase rollover commits with format from §8:
```
plan: phase 2 close — promote-and-reset
```

---

## §7. Density signals

Watch for content-aware signals:

| Signal | Action |
|---|---|
| Same fact stated three ways | Densify the section |
| Prose paragraph that could be a 4-row table | Convert to table |
| Reader can't find current state quickly (scroll past, or >~30s of scroll) | Restructure (move active to top, archive resolved) |
| Closing-phase debris (sub-tasks, commits, checkpoints) accumulating | Trigger promote-and-reset |
| Risks registered then resolved still in active section | Archive resolved or strikethrough |

Agent surfaces signals as one-liners: `"PLAN.md §6 has redundancy. Densify? [y/N]"`. User chooses; no enforcement.

---

## §8. Git commit conventions for plan changes

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

