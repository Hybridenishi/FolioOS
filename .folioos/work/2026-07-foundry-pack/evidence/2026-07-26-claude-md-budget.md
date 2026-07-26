# QA record — compiled `CLAUDE.md` budget check

- **Date:** 2026-07-26
- **Plan:** `.folioos/work/2026-07-foundry-pack/plan.md` (approved, `7989e1a3`)
- **Executes:** the plan's test-plan item "`CLAUDE.md` budget. Compile and count."
- **Also closes:** the gap deferred in
  `.folioos/work/2026-07-approval-ergonomics/evidence/2026-07-25-qa-extraction-and-drift.md`

## Result

**PASS — 110 lines against a hard budget of 150. 40 lines of headroom.**

| Section (per `adapters/claude-code/compile.md`) | Nominal | Actual |
|---|---|---|
| 1. Pointer block | ~10 | 13 |
| 2. Autonomy + work-state core | ~25 | 26 |
| 3. Working agreement core | ~10 | 12 |
| 4. Standards digest | ~60 | 41 |
| 5. Escalation map | ~15 | 14 |
| **Total** | **~120** | **110** |

Source always-layer: **344 raw lines → 110 compiled, 3.12× compression.**

## Method

Trial compile only — **no project has adopted this kernel**, so this resolves the cascade
for a hypothetical Foundry project: kernel ⊕ `pack:foundry-vtt`, no project overrides. Nine
`scope: always` documents (7 kernel, 2 pack). Distilled by hand per the compile procedure's
budget structure; rules preserved, prose justifications replaced by a path to the source.
The artifact was produced as scratch and is not committed — it is a build output with no
project to build into.

## Why the earlier concern was overstated

Two prior notes flagged this as a live risk, citing raw-line totals: 270 → 150 (1.80×) at
kernel 0.4.0, then 343 → 150 (2.28×) once the pack's two `always` standards existed.

**Both numbers measured the wrong thing.** The budget constrains the *compiled* artifact,
and distillation drops prose — which is most of what the pack standards added. Their rule
content is short; their rationale sections are long, and rationale compiles to a link by
design. Raw source length is therefore a weak predictor of compiled length, and rising raw
totals do not imply rising budget pressure.

The real constraint is **rule count**, not line count. On that measure the always-layer has
room: the standards digest, the section most at risk from a pack that adds standards, came
in at 41 lines against a 60-line allocation.

## What this does not prove

- **One pack.** A project needing two packs' worth of `always` standards is untested, and
  the document contract permits it. The headroom here is not evidence about that case.
- **No project overrides.** A real adoption adds `type: override` documents, and an
  `extend` on an `always`-scoped target lands in this layer.
- **Hand-distilled.** A future tooling implementation must reproduce the procedure, not
  merely hit the line count; a compiler that fits the budget by dropping rules passes this
  check and fails the intent.

## Bearing on the plan

Step 2's risk note — "the receipt rule must land as one bullet, or something must be
demoted" — resolves in favour of the bullet. `kernel/standards/code-quality.md` gained one
line for the read-back rule and nothing needed demoting. Step 6's adoption of
`foundryvtt-mcp` can compile against this kernel without a budget-driven redesign.
