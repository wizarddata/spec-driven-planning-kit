# Scoping rules

## Rules

| # | Rule |
|---|---|
| 1 | **Problem before solution.** Elicit narrative + concrete constraints (hardware, current tools, throughput, scale) before proposing stack. Refuse stack commit until grounded. |
| 2 | **Surface ≥3 options, quantified, then recommend.** Show LOC / cost / throughput / complexity tradeoffs. User sees design space, not just pick. |
| 3 | **Existing tools first.** Parallel research agents eval off-shelf before any custom build. "Build" is verdict, not default. |
| 4 | **Concept-only docs at planning.** Skip risk tables, LOC budgets, file scope, enums, schema. See Output section for SCOPE.md sections. |
| 5 | **Lock decisions one at time.** Each topic gets explicit user confirm ("lock", "skip MVP", "drop") before moving on. |
| 6 | **Push back honestly. Admit wrong fast.** Challenge bad ideas with reasons. Correct mistakes moment they surface. No saving face. |

## Caveman doc style

All kit-managed docs (`SCOPE.md`, `PLAN.md`, `PROGRESS.md`, AGENTS.md trap entries, research notes) drop articles + filler + hedging. Fragments OK. Technical terms exact. Tables / code blocks unchanged. Exemptions: code, commits, security warnings, irreversible-action confirmations stay normal English.

## Feature-gap pass

After initial scope locked, brainstorm likely-missed candidates before declaring `SCOPE.md` done. Walk each candidate one at time per Rule 5. Lock or drop.

## Output

`SCOPE.md` in project folder. Sections:

- one-line problem statement
- phases (one-liner each, no detail)
- stack (table)
- features in / features out
- locked decisions

## Project folder convention

```
<TARGET>_<PURPOSE>_<INTERFACE>/
├── SCOPE.md           # output of scoping-rules
└── README.md          # minimal pointer
```
