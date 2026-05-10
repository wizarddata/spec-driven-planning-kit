# Scoping rules

## Rules

| # | Rule |
|---|---|
| 1 | **Problem before solution.** Elicit narrative + concrete constraints (hardware, current tools, throughput, scale) before proposing any stack. Refuse to commit to a stack until grounded. |
| 2 | **Surface ≥3 options, quantified, then recommend.** Show LOC / cost / throughput / complexity tradeoffs. User sees the design space, not just the pick. |
| 3 | **Existing tools first.** Parallel research agents to eval off-shelf before any custom build. "Build" is a verdict, not a default. |
| 4 | **Concept-only docs at planning.** Skip risk tables, LOC budgets, file scope, glossary, enums, schema. See Output section for SCOPE.md sections. |
| 5 | **Lock decisions one at a time.** Each topic gets explicit user confirmation ("lock", "skip MVP", "drop") before moving on. |
| 6 | **Research as data, not prose.** Machine-readable JSON in `docs/research/<date>-findings.json`. Skip pretty markdown reports — they waste tokens on re-reads. |
| 7 | **Push back honestly. Admit wrong fast.** Challenge bad ideas with reasons. Correct mistakes the moment they surface. No saving face. |

## Feature-gap pass

After initial scope locked, brainstorm what's likely missed before declaring `SCOPE.md` done. Walk each candidate one at a time per Rule 5. Lock or drop.

## Output

`SCOPE.md` in the project folder. Sections:

- one-line problem statement
- phases (one-liner each, no detail)
- stack (table)
- features in / features out
- locked decisions
- pointer to `docs/research/` for raw findings

## Project folder convention

```
<TARGET>_<PURPOSE>_<INTERFACE>/
├── SCOPE.md           # output of scoping-rules
├── README.md          # minimal pointer
└── docs/research/     # machine-readable JSON findings
```
