# QA record — `CLAUDE.md` budget recheck after steps 3–5

- **Date:** 2026-07-26
- **Plan:** `.folioos/work/2026-07-foundry-pack/plan.md` (approved, `7989e1a3`)
- **Executes:** the plan's test-plan item "`CLAUDE.md` budget. Compile and count," rerun
  after steps 3–5 landed
- **Baseline:** `evidence/2026-07-26-claude-md-budget.md` (110 / 150, after step 2)

## Result

**PASS — 111 lines against the 150 hard budget. 39 lines of headroom.**

## What changed since the baseline

Of steps 3–5, only step 5 touches a `scope: always` document:

| Step | File | `scope` | Budget-relevant? |
|---|---|---|---|
| 3 | `kernel/contract/capability-model.md` | `on-demand` | No — not compiled into `CLAUDE.md` |
| 3 | `adapters/claude-code/capabilities.md` | n/a (adapter manifest, not a kernel doc) | No |
| 4 | `kernel/contract/document-contract.md` | `on-demand` | No |
| 5 | `kernel/standards/testing-policy.md` | `always` | **Yes** — gained one row |

Verified by checking frontmatter directly (not by grepping for the string `scope: always`,
which also appears inside `document-contract.md`'s example YAML block and would
false-positive):

```
kernel/contract/document-contract.md: scope: on-demand
kernel/standards/testing-policy.md:   scope: always
```

The nine-document always-layer from the baseline (7 kernel + 2 pack) is unchanged in
membership; `testing-policy.md` is one of the seven and grew by one table row.

## Method

Same trial-compile method as the baseline: hand-distillation per `adapters/claude-code/compile.md`'s
budget structure (rules survive as bullets, prose compiles to a source-path link), applied
only to the delta — the new testing-policy row — rather than re-deriving the full artifact,
since every other section of the baseline compile is untouched by steps 3–5.

The new row compiles to one bullet in the standards digest:

> External-protocol boundaries (unmockable transport/bridge layers): extract & unit-test
> the pure decision logic; **required**, one live smoke check per protocol operation,
> receipt recorded as evidence. → `kernel/standards/testing-policy.md`

| Section | Baseline actual | Delta | New actual | Nominal |
|---|---|---|---|---|
| 1. Pointer block | 13 | 0 | 13 | ~10 |
| 2. Autonomy + work-state core | 26 | 0 | 26 | ~25 |
| 3. Working agreement core | 12 | 0 | 12 | ~10 |
| 4. Standards digest | 41 | +1 | 42 | ~60 |
| 5. Escalation map | 14 | 0 | 14 | ~15 |
| **Total** | **110** | **+1** | **111** | **~120** |

## Bearing on the plan

Confirms the baseline's finding: rule *count*, not source line count, is the real
constraint, and it has room. One new testing-policy rule cost one compiled line. Step 9's
kernel minor bump can proceed without a budget-driven redesign.
