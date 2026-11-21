---
id: kernel/workflows/review-pipeline
type: workflow
layer: kernel
scope: on-review
requires: []
overridable: true
version: 2
---

# Workflow: Multi-Agent Review Pipeline

Specialized persona reviews → adversarial verification → one synthesized report for the human gate. The pipeline's value comes from **decorrelated evidence**, not role-play: each persona consumes different inputs (defined in each persona file), so their blind spots differ.

## Stages

1. **Select personas.** From the merged document set (kernel + active pack + project overrides):
   - **Always:** the active pack or project's primary domain/code engineer persona, plus `software-architect`.
   - **If user-facing UI changed:** the active UI/design persona (via [ux-review](ux-review.md)).
   - **If behavior is user-visible:** `product-manager`.
   - **Always for system features** (import/export, backup/restore, migrations, sync, published-contract builds), else risk-based: `qa-lead`.
   - **Always last:** `technical-writer` (utility tier — cheap, catches drift).
2. **Persona reviews** *(per project routing override; implementation tier default, `technical-writer` at utility tier)*. Each runs per its persona file, on *its* evidence only, producing severity-ranked findings (`blocker | should-fix | nit`) with `file:line` and a concrete failure scenario.
   - When `multi-provider` is available, dispatch personas across decorrelated providers using a 4-part self-contained brief (per [model-routing](../principles/model-routing.md)) that preserves strict evidence isolation.
   - Apply **Vendor-Split**: route the primary domain/code reviewer to a different vendor than the diff's author.
   - Enforce modality gates (e.g., vision-capable model for visual/UI personas).
   - Serialize any shared-state evidence capture (screenshots, live app walks, full suite runs) *before* launching parallel read-only reviewers.
3. **Adversarial verify** *(reasoning tier)*. Every `blocker`/`should-fix` finding gets an independent refutation attempt: "prove this finding wrong or confirm it with a concrete failure scenario." Findings that survive are `CONFIRMED`; unrefuted-but-undemonstrated are `PLAUSIBLE`; refuted ones are dropped with the refutation noted. On checkpoint-class invariant seams, run adversarial verify on a vendor different from the diff's author.
4. **Synthesize** *(reasoning tier)*. One report: confirmed findings ranked by severity, plausible findings flagged, cross-vendor disagreements surfaced explicitly (never silently adjudicated), the architect's "silent decisions" list, and the writer's regeneration worklist. Written to `evidence/<date>-review.md`.
5. **Human gate.** The human reads the synthesis, not the raw per-persona reviews.

## Boundary with formal cross-vendor review

Even when `multi-provider` already decorrelates Stage 2 personas across vendors, checkpoint-class changes (schemas, migrations, sync, release candidates, high-stakes plans) still use [cross-vendor-review.md](cross-vendor-review.md) to run an unanchored, zero-parent-history packet review and file the report verbatim to `evidence/<date>-crossvendor-<vendor>.md`.

## Rules

- Findings need a concrete failure scenario, not a vibe. "This could be fragile" doesn't survive verification.
- Fewer, verified findings beat many correlated ones — the pipeline optimizes signal, not volume.
- Recurring findings across changes are promotion candidates ("encode this as a checklist item") — note them for the DX engineer.

## Capability ladder
- **Preferred** (`requires: [multi-provider]`): personas run as parallel cross-provider turns with isolated evidence, Vendor-Split enforcement, and sequential pre-captured shared-state evidence; verify pass runs per-finding.
- **Standard** (`requires: [subagents]`): personas run as parallel same-provider subagents with isolated evidence; verify pass runs per-finding.
- **Fallback** (`requires: [file-system, shell]`): sequential passes in one context — one persona at a time, *emitting findings to the evidence file before starting the next hat* (prevents later hats laundering earlier conclusions). Verify pass reviews the combined list.
- **Floor** (`requires: [file-system]`): single pass against [checklists/pre-merge.md](../checklists/pre-merge.md) + the pack/project review checklists, explicitly labeled "single-reviewer, unverified" in the report.
