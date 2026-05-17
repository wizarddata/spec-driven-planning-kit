# Training-based simplification — fight points log

Branch: `training-based-simplification` off `granular-split`.

Goal: rework kit rules that fight fresh-Opus training instincts. Each entry = one friction point + simpler training-aligned fix.

## Decisions

```yaml
- id: D1
  topic: pipe tables banned (kit §0)
  intent: |
    Readability (long cells wrap) AND output discipline (YAML structural
    rigidity stops my prose-creep — user accepts personal readability cost
    in exchange for compression).
  fight: my default for any 2-axis data is pipe table
  fix: |
    Keep original ban. All structured data → YAML fence. No exceptions.
    Sniff-test carve-out (earlier draft) rejected — weakened the
    anti-prose-creep rule. YAML everywhere is the cleanest enforcement.
  status: agreed (reverts to original §0)
```

- id: D2
  topic: sweep-vs-bend matrix (kit §2)
  intent: force sweep consideration on every arch decision, catch chained bends
          around stale earlier decisions
  fight: |
    S/M/L bucketing + 3x3 matrix is theater. I fill cells to satisfy schema,
    pick verdict by vibes. Bucket lookup is not how my training weighs tradeoffs.
  fix: |
    Hybrid: pure-thinking rule + one structural field.

    Replace all of §2 with one line in PLAN conventions header:
      arch_decisions: "Before locking any decision that bends existing arch,
                       internally weigh the rewrite alternative. Lock the local
                       fix only if rewrite is concretely more expensive. Surface
                       both options to user when genuinely unsure."

    Lock row keeps ONE addition: `constraint:` tag.
      - n: 1
        decision: <short title>
        constraint: <tag>     # what's being bent around
        note: <one line>

    Constraint tag enables chain detector: before locking a bend, grep prior
    locks for same tag. Match → surface sweep candidate.
  what_dies:
    - bend/judge/sweep verdict labels
    - S/M/L cost+benefit definitions
    - 3x3 matrix
    - sweep_cost / sweep_benefit fields
    - default-M uncertainty tag
    - user-suggestion-mapping subclause (covered by "surface when unsure")
  status: agreed
```

- id: D3
  topic: stuck-prompts fire autonomously (kit §3)
  intent: force halt + sweep consideration at specific decision points
  fight: stateful triggers + interrupt-user behavior fights training defaults
  fix: |
    §3 arch-triggers (bucket-missing, sweep-attempted-bend, judge) absorbed
    into D2 — "surface when unsure" + chain detector covers them.
    Phase-completion nag deferred to phase_close_ritual fight point.
  status: agreed

- id: D4
  topic: conventions header inside PLAN.md (kit §4)
  intent: |
    Co-locate kit rules with arch spec so they always load on resume.
    YAML form (vs prose) acts as forcing function against my prose-creep.
  fight: |
    My instinct = extract 30-line YAML block to AGENTS.md for cleanliness.
    Kit guard blocks this. Real fix is shrinking the block, not relocating.
  fix: |
    Keep header in PLAN, keep YAML. Shrink from ~30 to ~18 lines.

    Strip (absorbed or noise):
      - sweep_vs_bend pointer (absorbed by arch_decisions one-liner from D2)
      - user_suggestion_mapping (covered by "surface when unsure")
      - stuck_prompts pointer (§3 mostly dies, D3)
      - commits.filter / commits.no_doc_log (procedural noise, not rules)
      - phase_close_ritual inline 8-step list (move to §6 body, header points)

    Keep (forcing functions I violate without local reminder):
      - pipe_tables (D1 sniff-test form)
      - arch_decisions (D2 one-liner)
      - lock_row schema
      - doc_style (what-not-why, structure, brief)
      - file_roles
      - commits (4 short patterns)
      - phase_close_ritual pointer to §6
  status: agreed

- id: D5
  topic: phase-close ritual + completion nag (kit §3 + §6)
  intent: user memory aid — prevent phases silently ending w/o close commit
  fight: |
    Nag: stateful suppress + per-turn scan fights goal (re-firing IS the feature
         when user forgot). Overhead.
    Ritual: my training drifts on long step sequences. I'll do 6 of 8 and stop —
            exactly the failure mode the rule guards against.
  fix: |
    Nag trigger: commit event, not periodic scan.
      After a commit I judge likely completes the phase (last sub-task or
      matches demo), surface close prompt at end of reply.
      No suppress logic. "not yet" drops for current reply only.
      Next phase-completing commit re-prompts. Re-firing is correct.

    Ritual execution: TaskList enforcement.
      Convention: agent MUST create TaskList from §6 steps, tick each.
      Skipped step = unticked task = visible to user.
      Maps to my training default (multi-step ops → Task tool).

    Updated header line:
      phase_close_ritual:
        trigger: user replies "close" OR manual fire
        execution: agent creates TaskList from §6 steps, ticks each, commits at end
        see: kit §6
  status: agreed

- id: D6
  topic: phase doc `reads:` field (kit §5b)
  intent: |
    Intentional belt-and-suspenders. User repeated load-instruction in phase
    doc because PLAN conventions alone wasn't getting honored.
    Also serves additive role: phase-specific extras beyond always-load set.
  fight: false-positive on my part — duplication is feature, not bug
  fix: no change. Keep as-is.
  status: agreed (no change)

- id: D7
  topic: commit conventions (kit §4 conventions header)
  intent: phase membership in subject line, pairs with kit workflow
  fight: my training bias toward Conventional Commits ≠ technical superiority
  fix: |
    Keep current four-pattern block. Conventional Commits offers no benefit
    unless project uses semver auto-bump tooling. Drift risk mitigated by
    conventions header reminder + my git log style mimicry.
  status: agreed (no change)

- id: D8
  topic: §4 PLAN.md section placeholders cryptic
  intent: force consistent section layout across PLANs + resume sessions
  fight: bare headers (no body, no guide) = no enforcement. I drift to whatever fits.
  fix: |
    Add one-line content guide per section + required marker.

    Template:
      ## §1. Glossary
      Domain terms used across PLAN. One line per term.

      ## §2. Closed enums
      Locked sets — state machines, type variants, mode flags.
      Lock values here; do not invent new ones in code.

      ## §3. Schema
      Data shapes — wire formats, DB schema, file formats, interface signatures.

      ## §4-N. Subsystems
      One section per arch component. Naming + ordering up to project.

    Conventions header addition:
      plan_sections:
        required: [§1 Glossary, §2 Closed enums, §3 Schema, §4-N Subsystems]
        rule: do not rename, do not drop numbering. Absent → "N/A" line.
  status: agreed

## Open fight points

```yaml
[]   # all 8 addressed
```
