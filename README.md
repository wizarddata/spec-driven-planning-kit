# spec-driven-planning-kit

Single-document portable kit for solo + AI planning of features and full projects.

## Contents

- [`spec-driven-planning-kit.md`](./spec-driven-planning-kit.md) — the kit itself

## Use

Until you fire a kickoff phrase, the agent treats requests normally — no kit overhead on simple fixes.

Kickoff phrases:
- `"let's plan this with the kit"`
- `"use the kit"`
- `"engage the kit"`

After kickoff, the agent runs the sweep-vs-bend lock loop, writes `PLAN.md` (with the "Kit conventions" header section auto-included), and (for multi-session features) maintains `PROGRESS.md`. Conventions live in those files; agent reads them on every resume.
| `PLAN.md` "Kit conventions" header | Auto-loaded with PLAN.md per feature | Operational rules (sweep-vs-bend matrix, commit format, promote-and-reset, density signals) — kit bakes them in. |

Agent reads PLAN.md whenever you resume a feature, so the conventions are always in context for any active feature.