---
id: kernel/workflows/review-pipeline
type: workflow
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Workflow: Multi-Agent Review Pipeline

Specialized persona reviews → adversarial verification → one synthesized report for the human gate. The pipeline's value comes from **decorrelated evidence**, not role-play: each persona consumes different inputs (defined in each persona file), so their blind spots differ.

## Stages

1. **Select personas.** Always: staff-ios-engineer, software-architect. If UI changed: hig-expert (via [ux-review](ux-review.md)). If behavior is user-visible: product-manager. Always for system features, else risk-based: qa-lead. Always last: technical-writer (cheap, catches drift).
2. **Persona reviews** *(implementation tier)*. Each runs per its persona file, on *its* evidence only, producing severity-ranked findings.
3. **Adversarial verify** *(reasoning tier)*. Every `blocker`/`should-fix` finding gets an independent refutation attempt: "prove this finding wrong or confirm it with a concrete failure scenario." Findings that survive are `CONFIRMED`; unrefuted-but-undemonstrated are `PLAUSIBLE`; refuted ones are dropped with the refutation noted. This pass exists because persona reviews share a base model and can be confidently, correlately wrong.
4. **Synthesize** *(reasoning tier)*. One report: confirmed findings ranked by severity, plausible findings flagged, the architect's "silent decisions" list, the writer's regeneration worklist. Written to `evidence/<date>-review.md`.
5. **Human gate.** The human reads the synthesis, not seven raw reviews.

## Rules

- Findings need a concrete failure scenario, not a vibe. "This could be fragile" doesn't survive verification.
- Fewer, verified findings beat many correlated ones — the pipeline optimizes signal, not volume.
- Recurring findings across changes are promotion candidates ("encode this as a checklist item") — note them for the DX engineer.

## Capability ladder
- **Preferred** (requires: subagents): personas run as parallel subagents with isolated evidence; verify pass runs per-finding.
- **Fallback** (requires: file-system, shell): sequential passes in one context — one persona at a time, *emitting findings to the evidence file before starting the next hat* (prevents later hats laundering earlier conclusions). Verify pass reviews the combined list.
- **Floor** (requires: file-system): single pass against [checklists/pre-merge.md](../checklists/pre-merge.md) + the pack review checklists, explicitly labeled "single-reviewer, unverified" in the report.
