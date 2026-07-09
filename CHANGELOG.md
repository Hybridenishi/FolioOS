# FolioOS Changelog

Every entry is a methodology diff: what changed about *how we work*, not just which files moved. Projects read this before adopting a new manifest release.

## 0.3.0 — 2026-07-09

**Cross-vendor review: a second AI opinion as a defined, evidence-first procedure.** Adds training-level decorrelation — the review pipeline already decorrelates by evidence (personas) and by adversarial verify; a different vendor's model adds the axis prompts can't reach, and sidesteps self-preference bias.

- **New workflow** `kernel/workflows/cross-vendor-review.md`: checkpoint-class work only; two modes (independent review — packet excludes the first agent's findings to prevent anchoring; findings verification — refute/confirm). Reports are append-only evidence; the synthesis surfaces **deltas** (agreement between vendors is low-information; disagreement is the product) and never silently adjudicates them. Built-in adoption experiment: after ~5 runs, if no delta ever changed a decision, record a candidate and stop — process must earn its maintenance.
- **New template** `kernel/templates/review-packet.md`: self-contained packet contract (the external reviewer has no session context), with explicit anchoring rules.
- **codex adapter reviewer slice** (0.1.0): calibration gate (R3 minimum), fixed prompts for both modes, invocation + report-filing procedure. Full codex adapter remains a stub.

Kernel 0.2.0 → 0.3.0 (minor: new workflow + template, no contract changes).

**Model calibration becomes a defined procedure.** Previously, model-routing said non-Claude models "map by capability equivalence" without saying how; now there's a protocol.

- **New workflow** `kernel/workflows/model-calibration.md`: qualify any model set (any vendor, open-source, local) into the routing tiers via probes. Placement = highest tier whose probes pass; route to the cheapest qualifier. Calibration is per (model × harness × config); ensembles (MoA stacks) calibrate as one unit. Placements are dated and perishable.
- **New probe suite** `kernel/calibration/` (9 probes + rubric): reasoning (trap-plan, pushback, adversarial-verify), implementation (faithful-execution, swift-fluency, re-gate discipline), utility (doc-regeneration, release-notes, format-exact ADR). Probes are `type: template` — deliberately no schema change.
- **model-routing.md** (v2): links the calibration protocol; adds the cheapest-qualifier placement rule and the ensemble-as-unit rule.
- **claude-code adapter** (0.2.0): gains the dated tier-mapping table (Opus/Sonnet/Haiku placement, basis: vendor lineup + usage).

Kernel 0.1.0 → 0.2.0 (minor: new workflow + documents, no contract changes). Pack unchanged.

## 0.1.0 — 2026-07-01

Initial release.

- **Contracts:** document contract (frontmatter schema + cascade merge semantics), capability model (abstract capabilities + fallback ladders), work-state (plan lifecycle with approval-by-hash).
- **Principles:** working agreement (collaborative, pushback mandated), autonomy model (plan → approve → execute with re-gate rule and checkpoint actions), model routing (reasoning / implementation / utility tiers).
- **Personas:** seven reviewer roles, each defined by decorrelated evidence inputs, not just prompt framing.
- **Standards:** code quality, git workflow (trunk + short-lived PR-gated branches), security & privacy, accessibility, testing policy.
- **Workflows:** feature lifecycle, multi-agent review pipeline (with sequential and single-pass fallbacks), UX review, QA, release (manual Xcode with Fastlane seams), knowledge promotion.
- **Templates:** plan, PRD, ADR, RFC, release notes, PR description, QA run, doc page (with `covers:` drift tracking).
- **Pack ios-swift:** SwiftUI conventions, Swift concurrency, app architecture default (MV + @Observable), OS targeting policy (N−1), review/App Store checklists, HIG cheatsheet, common pitfalls.
- **Adapter claude-code:** capability manifest and compile procedure (scope-driven, 150-line always-loaded budget).
- **Userland:** project template with overrides/, decisions/, work/, candidates/.
