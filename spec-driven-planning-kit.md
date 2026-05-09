# Spec-Driven Planning Kit

Single-document portable kit for solo + AI planning of features and full projects. Captures the lock-iteration + AI-dense rewrite + structured-progress paradigm.

**Tool agnosticism:** This kit names files concretely — `AGENTS.md`, `PLAN.md`, `PROGRESS.md`, `Constitution.md`. If your tool reads a different agent-instruction file (`CLAUDE.md`, `.cursorrules`, `GEMINI.md`), substitute the name everywhere. Mechanics are identical.

---

## §0. Install bootstrap (runs on every kickoff)

Kit considers itself **uninstalled** until the §9 snippet lives in the user's global agent-instruction file (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`, `~/.cursor/.cursorrules`, etc.). Until installed, kickoff phrases (§2) trigger install — not lock-iteration.

On every kickoff, agent runs this check **first**:

1. Grep the global agent-instruction file for marker `<!-- kit:installed:v1 -->`.
2. If marker present → kit installed, proceed to §2 kickoff handling.
3. If marker absent → STOP. Prompt user:

   > Spec-driven planning kit not installed in your global agent-instruction file. Install §9 snippet now (one-time per machine, ~50 lines)? [y/N]

4. On `y`:
   - Append §9 snippet block to the global agent-instruction file.
   - Edit the path on line 2 to point at wherever the kit was actually cloned (the snippet ships with `~/Documents/spec-driven-planning-kit/spec-driven-planning-kit.md` as a placeholder).
   - If global file does not exist, create it.
   - Print one-line confirm: `kit installed → ~/.claude/CLAUDE.md (or wherever)`. Then resume the kickoff phrase as if just received.
5. On `n` → abort kit, treat the request as normal (no kit mechanics this session).

After install, every future session auto-loads the snippet (2 lines) via the global agent-instruction file. The §0 check still fires once per kickoff (cheap grep), almost always passes silently. **Per-load cost: 2 lines.** Operational rules don't live in CLAUDE.md — they live in §6 / §7 templates that get baked into every PLAN.md and PROGRESS.md the kit writes, and the agent reads PLAN/PROGRESS on every resume anyway.

**Marker format:** literal HTML comment `<!-- kit:installed:v1 -->` on its own line as the first line of the appended snippet block. Version suffix lets future kit revisions (`v2`, `v3`) detect + offer to upgrade by re-pasting.

**First-time discovery:** the agent only knows about the kit if it has either (a) already loaded the snippet from a prior install, or (b) been pointed at the kit file by the user (e.g. `"use the kit at ~/path/to/spec-driven-planning-kit.md"`). Path (b) is the bootstrapping case for a fresh machine: agent reads the kit file once, sees this §0, runs the prompt, installs. From then on path (a) covers every session.

---

## §1. When to use

Three sizes:

| Size | Trigger | Kit pieces |
|---|---|---|
| Trivial fix | ≤2 files, no architecture impact, mechanical edit | **Skip kit.** Just edit. |
| Feature (Mode A) | 3+ files OR architecture impact OR new module in existing project | PLAN.md, optionally PROGRESS.md if multi-session |
| Greenfield project (Mode B) | Empty repo OR new project | Constitution.md + PLAN.md + PROGRESS.md, Phase 0 = bootstrap |

Right-sizing is the agent's first job after kickoff.

---

## §2. Kickoff (explicit only — no auto-fire)

User invokes deliberately. Until kickoff, treat all requests normally.

Kickoff phrases:
- `/kit <feature>` (when slash commands available)
- `"let's plan this with the kit"`
- `"use the kit"`
- `"engage the kit"`

**On every kickoff, §0 install bootstrap runs FIRST.** If kit is uninstalled (marker absent from global agent-instruction file), agent prompts to install before doing anything else; no lock-iteration, no PLAN.md write, no kit mechanics until install completes (or user declines, in which case kit aborts for this session).

After kickoff (and successful install if needed), ALL mechanics fire automatically until plan locked + implementation complete.

---

## §3. Two prompts that do ~80% of the format-shaping

Stated up front because they're load-bearing.

**Prompt #1 — anchor recommendations on industry convention:**
> *"Give me your recommendation based on industry standards and AI workflow best practices."*

Used implicitly by the agent on every lock recommendation (see §4 mandatory format). Stated explicitly only when agent's first pass feels improvised.

**Prompt #2 — densify the spec once locks settle:**
> *"Prioritize AI compatibility formatting, human readability is not required."*

Auto-fired by agent when `locks_remaining: 0`. Rewrites prose plan → YAML / closed enums / JSONC schema. Prevents subagents from re-litigating settled locks across sessions.

---

## §4. Lock-iteration loop

Plan a feature one decision at a time. Agent emits one lock per turn until all settle.

**Mandatory recommendation format:**

```
Decision: <one-line question>
Recommendation: <choice>
Convention basis: <tag>
Tradeoff: <one-line: what you give up>
```

Convention basis is a **tag-only** citation: `"ADR (Nygard 2011)"`, `"12-factor §III"`, `"AGENTS.md spec (Codex/Aider/Claude)"`, `"JSON Schema + closed enums (k8s API ref)"`, or `"no direct convention; closest analog: X"`.

Two source bodies to cite from:

| Body | Examples |
|---|---|
| Traditional software | RFC, ADR, arc42, 12-factor, OpenAPI, JSON Schema, semver, Conventional Commits, REST, ISO 31000, C4 model |
| AI workflow | AGENTS.md, MCP, eval-driven dev, prompt caching, tool-use schemas, GitHub Spec Kit, retrieval patterns |

**User reply pattern:** `use your recommendation` (lock as-is) OR `<override>` (state alternative + reason).

**User-suggestion mapping rule:** when user proposes a solution mid-iteration (not just answering Y/N), agent maps to convention BEFORE evaluating:

```
You suggested: <user's idea>
Industry pattern match: <pattern X if exists | "no match — novel territory">
Source: <citation>
Equivalent / closest fit: <how the pattern would solve the same problem>
Recommendation: <adopt | modify | proceed novel and document why>
```

User cannot accidentally reinvent a known wheel without the agent surfacing it.

**Termination:** when `locks_remaining: 0`, agent fires Prompt #2 (AI-dense rewrite) automatically and writes the spec.

---

## §5. Stuck-prompts (when locks won't settle)

Use when 2+ rounds pass with no progress on a single decision.

| # | Prompt | When to fire |
|---|---|---|
| 1 | **Assumption reframe** — *"How do popular [X] handle this?"* | First-pass recommendation feels improvised, novel, or filename-map-tier |
| 2 | **No-compat lock** — *"Don't accommodate old code if it compromises our goals."* | User keeps hedging on legacy / migration / "but the existing system" |
| 3 | **Convention audit** — *"What's the existing industry pattern for this? Name source. If novel, name closest analog and justify divergence."* | Recommendation lacks convention basis or basis is weak |
| 4 | **AI-dense rewrite** — *"Prioritize AI compatibility formatting, human readability is not required."* | Settled but spec reads like prose; densify before commit |

Agent fires these autonomously after detecting stall. User can also fire any of them manually.

---

## §6. PLAN.md template (Mode A — feature in existing project)

```markdown
# <feature> — Plan

> **Decision rationale lives in git log.** Run `git log -- PLAN.md --grep="lock #N"` to recover the why-history of any lock.

```yaml
status: planning | active | done
revised: YYYY-MM-DD
locks_settled: 0
locks_remaining: ?
```

## Kit conventions (load-bearing — do not delete)

> Every PLAN.md emitted by spec-driven-planning-kit carries this header verbatim. Agent reads it on every resume; conventions stay live without bloating CLAUDE.md.

- **Lock recommendation format** (every recommendation):
    ```
    Decision: <one-line question>
    Recommendation: <choice>
    Convention basis: <tag>     (e.g. "ADR (Nygard 2011)", "12-factor §III", "no direct convention; closest analog: X")
    Tradeoff: <one-line: what you give up>
    ```
- **User-suggestion mapping** (when user proposes mid-iteration):
    ```
    You suggested: <user's idea>
    Industry pattern match: <pattern X | "no match — novel territory">
    Source: <citation>
    Equivalent / closest fit: <how the pattern would solve the same problem>
    Recommendation: <adopt | modify | proceed novel and document why>
    ```
- **Plan-edit commit format**:
    - `plan: lock #N — <decision>` (with body listing convention basis, alternatives considered, tradeoff)
    - `plan: lock #N revised — <old> → <new>` (with reason + reference to original commit)
    - `plan: phase <N> close — promote-and-reset` (lists what was promoted to risks / traps / discarded)
- **Promote-and-reset trigger**: at every phase close, agent migrates mid-impl decisions/surprises to PLAN.md risk register or AGENTS.md trap entries; discards sub-task checklist + commit log block; collapses state-of-branch to 1-line phase plan entry; resets PROGRESS.md to next-phase template; commits as above.
- **AI-dense rewrite trigger**: when `locks_remaining: 0`, agent fires "Prioritize AI compatibility formatting, human readability is not required." and converts prose plan to YAML / closed enums / JSONC schema before commit.
- **Stuck-prompts** (after 2+ stalled rounds on a single decision): assumption reframe, no-compat lock, convention audit, AI-dense rewrite — agent fires autonomously.
- **Density signals** (agent surfaces, user decides): same fact stated 3 ways → densify; reader scrolls past current state → restructure; closing-phase debris accumulating → trigger promote-and-reset.

## §0. Locks tally

| # | Decision | Outcome | Basis |
|---|---|---|---|
| 1 | <name> | <one-line lock> | <tag> |

## §1. Glossary

5-15 named concepts, one line each. Domain vocabulary the spec uses without re-defining.

## §2. Closed enums

Every union type, exhaustively listed. Anti-hallucination guardrail.

## §3. Schema

Canonical data shape. JSONC with comments OK. Discriminated unions explicit.

## §4-N. Subsystems

One section per concern. Bounded by domain, not arbitrary count.

## Phase plan

```yaml
phase_1: { ships: [...], demo: "<one-sentence success criterion>" }
phase_2: ...
```

Demo = unambiguous "done" check, not vague "feature complete."

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

## §7. PROGRESS.md template (active phase only)

```markdown
# <feature> — Progress

> **Conventions live in PLAN.md "Kit conventions" header.** Agent reads PLAN before resuming work; conventions auto-load with it. Do not duplicate here.

```yaml
current_phase: 1
current_branch: ?
started: YYYY-MM-DD
```

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

PROGRESS.md never accumulates closed phases. See §10 promote-and-reset.

---

## §8. Constitution.md template (Mode B — greenfield only)

```markdown
# <project> — Constitution

```yaml
status: locked
revised: YYYY-MM-DD
```

Project invariants. Locked at project start; cross-feature foundation.

## Stack-level decisions

| # | Area | Choice | Basis |
|---|---|---|---|
| 1 | Language | <e.g. TypeScript> | <tag> |
| 2 | Framework | <e.g. Vite> | <tag> |
| 3 | Hosting | <e.g. Cloudflare Workers> | <tag> |
| 4 | Storage | <e.g. SQLite> | <tag> |
| 5 | Test runner | <e.g. node --test> | <tag> |
| 6 | Lint | <e.g. eslint + project rules> | <tag> |
| 7 | CI | <e.g. GitHub Actions> | <tag> |
| 8 | Commit format | <e.g. Conventional Commits> | <tag> |

## Cross-feature invariants

Architectural rules that hold across all features. Examples:
- Determinism rules (if applicable)
- Performance budgets
- Security constraints
- Data flow rules (e.g. "renderer reads sim, never writes")
```

In Mode B, Phase 0 of PLAN.md = bootstrap (build the substrate prereqs in §11).

---

## §9. Global agent-instruction snippet (minimal pointer)

Appended **once per machine** to the user's global agent-instruction file (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`, `~/.cursor/.cursorrules`, etc.). Auto-install happens via §0 bootstrap on first kickoff phrase; user does not paste manually.

The marker is mandatory — `§0` greps for it to detect "installed" state. Edit the path on the second line at install time to point at wherever the user cloned the kit.

```markdown
<!-- kit:installed:v1 -->
Spec-driven planning kit at `~/Documents/spec-driven-planning-kit/spec-driven-planning-kit.md`. Kickoff: `/kit <feature>`, `use the kit`, `let's plan this with the kit`, `engage the kit`. On any kickoff phrase, read the kit file and follow §0..§13. Until kickoff: no kit, no PLAN.md, no overhead.
```

That's the entire snippet — 2 lines. The operational rules (recommendation format, user-suggestion mapping, promote-and-reset, plan-edit commit format, density signals) are not duplicated here — they live in the kit body and, more importantly, get baked into every PLAN.md and PROGRESS.md the kit produces (see §6 and §7 templates' "Kit conventions (load-bearing)" header section). Agent reads PLAN/PROGRESS on every resume, so the conventions are always in context for any feature already in flight without bloating CLAUDE.md.

---

## §10. Promote-and-reset mechanic

When phase closes, agent runs autonomously:

1. **Mid-impl decisions / surprises** → migrate to PLAN.md risk register OR AGENTS.md trap entries (whichever fits).
2. **Sub-task checklist** → discard. `git log` is canonical.
3. **Commit log block** → discard. `git log` is canonical.
4. **State-of-branch checkpoint** → collapse to 1-line phase plan entry in PLAN.md (`Phase 2 shipped: <demo>`).
5. **PROGRESS.md** → reset to next phase template (header + empty sub-tasks).

Phase rollover commits with format from §13:
```
plan: phase 2 close — promote-and-reset
```

PROGRESS.md never accumulates. No archive files. Valuable insights live where they're load-bearing (PLAN risks, AGENTS traps); rest is git history.

---

## §11. Substrate prereqs

Format alone is ~25% of why this kit produces good outcomes. Other ~75% spread across substrate. Without prereqs, kit underperforms.

Greenfield Mode B Phase 0 = build these. Mode A assumes they exist; verify before starting.

| Prereq | Why it matters | Counterfactual |
|---|---|---|
| Fast test runner (<10s, ideally <5s for unit tier) | Iteration loop survives | Slow tests → tests get skipped → drift compounds |
| Lint with project-specific rules | Conventions auto-enforced | Subagents reinvent conventions every session |
| AGENTS.md / equivalent with project invariants + traps | Cross-session context | Subagents lose load-bearing context, repeat fixed bugs |
| Git with structured commit messages (this kit's format) | Decision rationale durable | Why-history dies with chat history |
| User discipline: terse corrections, trust agent recommendations, no scope creep | Lock-iteration converges | Re-litigation, decision drift, scope inflation |

Verify before kickoff. If missing, surface as Phase 0 task.

---

## §12. Density signals (when to densify or restructure)

Length numbers are not in this kit on purpose — domain dictates length, not arbitrary caps. Watch for content-aware signals:

| Signal | Action |
|---|---|
| Same fact stated three ways | Densify the section |
| Prose paragraph that could be a 4-row table | Convert to table |
| Section reader scrolls past to find current state | Restructure (move active to top, archive resolved) |
| Closing-phase debris (sub-tasks, commits, checkpoints) accumulating | Trigger promote-and-reset |
| Risks registered then resolved still in active section | Archive resolved or strikethrough |
| Reader can't find current state in ~30 seconds of scroll | Restructure |

Agent surfaces signals as one-liners: `"PLAN.md §6 has redundancy. Densify? [y/N]"`. User chooses; no enforcement.

---

## §13. Git commit conventions for plan changes

PLAN.md changes commit separately from code (one logical change per commit).

**New lock:**
```
plan: lock #N — <decision>

Convention basis: <tag>
Alternatives considered:
- <alt 1> — <rejected because>
- <alt 2> — <rejected because>
Tradeoff: <what we give up>
```

**Lock revision:**
```
plan: lock #N revised — <old> → <new>

Reason: <new evidence / changed constraint>
Original rationale: see commit <hash>
```

**Phase close:**
```
plan: phase <N> close — promote-and-reset

Promoted to PLAN.md risk register:
- <decision> — <why load-bearing>
Promoted to AGENTS.md trap entries:
- <trap> — <why future readers need it>
Discarded (git log canonical):
- Sub-task checklist
- Commit log
- State-of-branch (collapsed to phase plan one-liner)
```

**Milestone tags:** `git tag plan-v1` after locks first settle. `git tag plan-v2` after major lock-set revision. Provides searchable milestones.

Search: `git log -- PLAN.md --grep="lock #N"` reconstructs why-history of any decision.

---

## §14. Counterfactuals — what fails without each piece

Empirical guidance for which pieces to fight for in resistant teams.

| Piece dropped | What fails |
|---|---|
| Convention citation | Locks drift novel without grounding; reinvention compounds |
| Lock-iteration loop | Decisions get batched 5-at-a-time; user skims; half settle silently |
| AI-dense rewrite | Plan stays prose; subagents re-litigate locks every session |
| Promote-and-reset | PROGRESS bloats; current state hard to find; tokens compound on resume |
| Explicit kickoff | False positives on simple fixes; documentation overhead on trivial edits |
| Substrate prereqs | Format is shiny but unsupported; drift compounds; format gets blamed |
| Git rationale commits | Why-history dies with chat; future-you reads tally with no basis |
| User-suggestion mapping | User reinvents wheel; agent rubber-stamps; novel territory unflagged |

Format alone, without substrate, lasts ~3 weeks before fraying. Substrate without format works fine but planning re-litigates each session. Multiplier — both needed.

---

## §15. Known limitations

Be deliberate about the kit's blind spots.

- **Sample size 1.** Novel mechanics (lock-iteration, promote-and-reset, convention citation, user-suggestion mapping) emerged from one project. May not generalize perfectly to all domains. Treat novel pieces as guidelines, revise based on outcomes.
- **Solo + AI optimized.** Team review lifecycle (proposed → reviewed → accepted) absent. If team grows, add `status: proposed | reviewed | active | done` to lock rows.
- **Tag-only citations are opaque to outsiders.** "ADR" doesn't expand for new readers. If external readers expected, add a footnote table mapping tags → full citations.
- **Length numbers omitted.** Some readers want a number to anchor on; observation from one project: `~982 lines felt heavy, below ~800 stayed scannable.` Reference, not rule.
- **Locks tally is consolidated.** ADR-per-file gives per-decision audit; tally collapses. Mitigation: structured git commits restore it on demand.

---

## §16. Two prompts to save for next project

If you copy nothing else from this kit, save these:

1. *"Give me your recommendation based on industry standards and AI workflow best practices."*
2. *"Prioritize AI compatibility formatting, human readability is not required."*

Fire #1 to anchor on conventions during planning. Fire #2 to densify after locks settle. These two did most of the work; everything else is structural support.
