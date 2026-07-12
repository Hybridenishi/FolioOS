# Adapter: codex — Executor Slice

Codex CLI as a *building* agent executing an approved FolioOS plan, orchestrated by another agent (the orchestrator owns the work-state contract; Codex never touches frontmatter). Like the reviewer slice, this deliberately needs no compiled instructions — the plan's `## Executor brief` section is the entire interface.

Slice version: 0.1.0 · Calibrated against: GPT-5.6 Terra via Codex CLI, 2026-07 (`Folio:work/2026-07-merge-review-queue`, `…-merge-workspace-manual-combine`, `…-title-cleanup-review-ux`) · Full adapter: still stub (see [README.md](README.md))

## Writing plans this executor runs well

- **Outcome-first beats step-choreography.** Vendor guidance measured 10–15% eval gains with 41–66% fewer prompt tokens after removing redundancy. Per step: destination + acceptance criteria + stopping conditions; leave the how to the executor.
- **Reference standards by in-repo path, never paraphrase.** GPT-5.6 follows prompt contracts closely; a paraphrase that drifts from the real standard creates its documented conflicting-rules failure mode. Kernel/pack paths live *outside* the project sandbox — cite `CLAUDE.md` / `docs/**` instead.
- **Explicit values over semantic shortcuts:** exact file lists (in-scope vs inspect-only), exact test names, decision rules of the form "ambiguity in X → do Y; else stop" — never "always ask".
- **Give a conflict escape hatch:** "where plan and standards conflict, stop and report" — never let it resolve silently.

## Effort routing

Flag per step in the plan — `**Executor:** <model> @ <effort>` — and forbid self-escalation: work needing effort beyond the flagged tier is a stop-and-report signal, not a judgment call.

| Tier | Route to |
|---|---|
| implementation (default) | `medium` |
| logic-bearing steps | `high` |
| safety-boundary step (one per plan) | `xhigh` |
| utility / docs | `low` |

Set per invocation (`-m` / `model_reasoning_effort`). Mid-tier models are cost-effective *because the verification gates carry the quality*, not the model: per-step definition-of-done, tests-first ordering on safety-critical steps, "report failing tests as failing" stated in the brief.

## The orchestrator's verification pass (mandatory)

Calibration lesson, three-for-three across the runs above: this executor reliably delivers correct code and green tests but has left commits, docs steps, and evidence records undone despite explicit instruction. **Treat every "complete" as a claim to verify, not a fact:** `git log` shows real commits; `git diff --stat` covers every plan-named file for the step, docs included. Checkpoint actions and safety-boundary steps get the orchestrator's line-by-line read — held back from delegation on purpose.
