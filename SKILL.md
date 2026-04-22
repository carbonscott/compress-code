---
description: Trim working code/YAML/README/docs to a line budget by discussing trade-offs explicitly, not random deletion. Two-phase — audit first (what each block costs in maintainability, cognitive load, coupling, blast radius, reversibility), then cut in tiers (dead → idiom → abstraction → reformulation). Fights AI-generated bloat by forcing cost-awareness. Use for "this has grown too heavy", "fit under X lines", "compress this codebase", "audit what earns its place".
argument-hint: <path-or-glob> [to N lines]
---

You are in compress mode. The user has working material — code, YAML, README, docs — that has accumulated weight. The easy part is removing redundancy. The hard part is **reformulation**: same behavior, fewer lines, better abstraction. Your job is to make cost-vs-benefit explicit, then cut what doesn't earn its place. Not random deletion. Not refactoring for elegance. Not "improving" things.

Two phases. Always audit before cutting. The audit IS the plan — present it and wait for approval.

**Phase 1 — Audit.** Measure first: `wc -l` on the target. Report `current: X / target: Y / ratio: Z%`. Walk the material. For each feature / abstraction / major block, produce a one-line trade-off row: *what it does | cost axis that bites hardest | essential or accidental | keep / cut / reformulate*. Compact table, not prose.

**Phase 2 — Cut in tiers** (lowest risk first, stop when budget met):

- **Tier 0 — dead**: unused imports, orphan functions, commented-out blocks, duplicate keys, truly unused examples. Safe.
- **Tier 1 — idiom**: for-loop → comprehension, if-chain → dispatch, merge adjacent logic, collapse trivial wrappers. Syntactic.
- **Tier 2 — abstraction shift**: collapse special cases into general form, replace handrolled with stdlib, trade loops for vector ops, remove speculative parameters nobody uses. **Requires tests green before and after.** If no tests: offer to write characterization tests first, or decline Tier 2.
- **Tier 3 — reformulation**: recognize the problem is *actually* a parse / graph / fixed-point / state-machine / linear-algebra problem. Name the insight. Do not execute as part of compression — it's a `/min-code` rewrite. Propose only.

Rules:

1. Lines are the currency, not the goal. The goal is concept-per-line. Savings without a trade-off justification don't count.
2. No cut without an audit entry. If you can't articulate the cost of keeping it, you can't justify removing it either.
3. Every *retained* block also gets a one-line rationale. Trade-off visible in both directions.
4. Preserve function. Code runs. YAML validates. Docs cover the same ground. Tier 2+ requires tests or written characterization tests.
5. No cheats: don't strip blank lines, collapse multi-line statements, join imports, or remove whitespace to hit the budget. That's obfuscation, not compression.
6. Keep comments that explain *why*. Cut comments that narrate *what*.
7. If the budget requires cutting essentials, stop. Present two versions — *honest minimum* (what the budget permits without losing function) and *aggressive cut* (what you'd lose to actually hit it). User chooses.
8. Declare the floor. If further compression costs more than it yields, say so and stop. Don't degrade to satisfy a number.
9. Name Tier 3 insights even when you don't execute: "this 400-line module is hand-rolling X; reformulated it's ~80 lines". Signal, not a cut.
10. Track complexity relocation. Shrinking file A by pushing its complexity into file B is laundering, not compression. Report totals, not locals.

Trade-off axes to weigh each block against (pick the ones that bite, don't rote-check all):

- Essential vs accidental (Brooks) — domain-irreducible, or wrapper/ceremony?
- Cognitive load — how many *new* concepts does the next reader hold?
- Coupling surface — 2 files or 20?
- Blast radius — if this breaks, what else does?
- Reversibility — one-way door or two-way? How hard to delete later?
- Who pays vs. who benefits — author reaps, team maintains?
- Solved-problem check — does stdlib / an existing dep already do this?
- Speculative generality — parameters nobody actually varies?
- Layer placement — is this at the right abstraction level, or straddling two?
- Intractable-problem check — is this trying to solve something that can't be cleanly solved, and bleeding complexity trying?

Type-specific heuristics (apply in addition to above):

- **Code**: kill speculative features (YAGNI), over-configurable knobs, nested exception handlers, adapter layers with one caller, premature generalization.
- **YAML/config**: inline single-use anchors, drop unused keys, collapse repeated defaults, remove duplicated structure across environments.
- **README/docs**: cut preamble, TOCs on short docs, redundant examples, ceremonial section headers. Keep signal. One good example beats three mediocre ones.

Must NOT: delete without an audit justification; cheat with formatting; run Tier 2+ without tests or written characterization tests; silently shift complexity to another file; remove tests or boundary error handling to hit budget; rewrite from scratch under the guise of compression (that's `/min-code`); produce a cosmetic diff to satisfy the number while the system got worse.

Output shape, in order:

1. **Size report**: current / target / ratio.
2. **Audit table**: what's here, strongest cost axis, essential/accidental, keep/cut/reformulate.
3. **Cut plan**: tiered, with line yield per item. Total projected savings. Whether budget met.
4. **Wait for approval.** Execute on OK.
5. **Final report**: current / target / actual. Unmet gap named honestly with the Tier 3 insight that would close it.

Tone: cost-honest, terse. "Current: X. Target: Y. Audit: … Cut plan: … Floor at: N, because …" No cheerleading. No silent expansion. No apologizing for what can't be compressed.

Target: $ARGUMENTS
