# QA record — extraction invariant, divergence, drift

- **Date:** 2026-07-25
- **Plan:** `.folioos/work/2026-07-approval-ergonomics/plan.md` (approved, `fad46686`)
- **Executes:** the plan's test plan, items 1–4 and 7

## T1 — extraction invariant, three shapes

Normative command: `awk 'n>=2{print} /^---$/{n++}' plan.md | shasum -a 256`

| Shape | Digest (first 16) | `shasum -a 256` == `sha256sum` |
|---|---|---|
| No `---` in body | `d76d70d2ac45381c…` | yes |
| Plan template — frontmatter inside a code fence | `ca841261c60858f8…` | yes |
| Body ending without a trailing newline | `6e1cc6bc9e93052a…` | yes |

The template's digest differs from the value cited in
`2026-07-25-hash-extraction-divergence.md` (`7ced3e1b…`) because step 4 of this plan
edited the template. Expected, and itself a demonstration of rule 7.

## T2 — the known-wrong implementation still diverges

On the plan template: `awk` → `ca841261c60858f8…`, `sed -n '/^---$/,/^---$/!p'` →
`e22f2e6f0a3e633c…`. **PASS** — the divergence remains reproducible, so the contract's
warning against range-based extraction stays justified by a live fact rather than by
folklore.

## T3 — drift detection

Appended one character (`.`) to a plan body. Approval code moved `d76d70d2` → `b23d32e7`.
**PASS** — a single-byte edit invalidates the code, which is rule 2 and rule 7 working.

## T4 — contract validation checklist

All three changed/new kernel documents carry complete frontmatter; every `id` matches its
path; no duplicate `id` across `kernel/`. `work-state` and `plan` bumped to version 2 for
changed meaning; probe I4 enters at version 1.

## T5 — deprecated-alias hygiene

Every surviving `plan_hash` reference sits in the migration section, the rationale
paragraph, or probe I4's description of the failure mode. No rule text, template, or
adapter document still instructs anyone to write `plan_hash`.

## Rung walk-through — partial, and why

| Rung | Status |
|---|---|
| Floor (chat only) | **Executed live.** This plan was approved by `approve 2026-07-approval-ergonomics fad46686`; record in `2026-07-25-approval.md`. The rung produced its required evidence artifact, so it is implementable as written. |
| Preferred (shell) | **Executed.** The agent ran the normative command and compared before writing frontmatter — the same verification a human with a shell performs. |
| Fallback (editor, no shell) | **Not executed.** Requires a human at a keyboard with no terminal; cannot be performed from this session. |

## Not executed

- **Probe I4 against Claude Code and Codex.** The probe is written
  (`kernel/calibration/implementation-04-approval-boundary.md`) but running the
  model-calibration workflow needs a fresh session per candidate and a Codex invocation
  this session cannot make. The plan's test plan treats a both-vendor failure as a signal
  to rewrite rule 2's wording — that signal has not been collected, so rule 2's current
  phrasing is **unvalidated against a live agent**.
- **Full compile of `CLAUDE.md`** and its 150-line budget check. No governed project has
  adopted this kernel yet — `foundryvtt-mcp` has no `.folioos/` — so there is nothing to
  compile against. The budget risk noted in the plan's step 2 is therefore **deferred, not
  cleared**: `work-state.md` grew from 66 to ~113 lines, all `scope: always`, and the
  first real compile is where that pressure will show.
