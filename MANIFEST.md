# FolioOS Release Manifest

The tested combination of component versions. Projects pin a manifest release, not individual components.

## Release 0.7.0 — 2026-11-20

| Component | Version | Notes |
|---|---|---|
| kernel | 0.6.0 | **contract change** — `capability-model` (v3) adds `multi-provider`. `model-routing` (v3) adds reasoning `effort` axis (promoting candidate `2026-07-25-effort-as-routing-axis`), extensible work-shape starter catalog, capability/evidence exclusions, Vendor-Split, Two-Strike escalation, 4-part delegation brief, mandatory Lead verification pass, and parallel vs. shared-state sequential seams. New `routing-discovery` workflow (v1). `review-pipeline` (v2), `cross-vendor-review` (v2), and `model-calibration` (v2) remove Swift/iOS leaks and support `multi-provider` + `effort`. `templates/plan` (v3) renames step `Effort` → `Size`. `git-workflow` (v2) adds multi-provider authorship & trailer rule. See [ADR-0009](.folioos/decisions/0009-multi-provider-routing-and-orchestration.md). |
| pack: foundry-vtt | 0.1.0 | unchanged |
| pack: ios-swift | 0.2.0 | unchanged |
| adapter: claude-code | 0.3.0 | capability manifest adds `multi-provider` (⚙️) and dated `(tier, effort)` table; compile procedure adds compact `CLAUDE.md` routing pointer, multi-provider `/folio-review` dispatch, and persona `model:` compilation |
| adapter: t3-code | 0.1.0 (orchestrator slice) | **new** — lightweight orchestrator slice mapping `multi-provider` onto T3 Code (`orchestrator_capabilities`, `delegate_task`, `isolation: "worktree"`, shared-state serialization, Lead verification) |
| adapter: codex | 0.2.0 (reviewer + executor slices) | unchanged |
| adapter: gemini | stub | unchanged |

## Release 0.6.0 — 2026-07-26

| Component | Version | Notes |
|---|---|---|
| kernel | 0.5.0 | **contract change** — capability vocabulary gains `live-environment`; capability-model rule 1 gains a narrow named exception (artifact-and-stop floor) for workflows requiring it. Document contract makes explicit that packs may contribute personas. Testing policy gains a row for unmockable external-protocol boundaries. See [ADR-0008](.folioos/decisions/0008-second-pack-kernel-boundaries.md). |
| pack: foundry-vtt | 0.1.0 | **new** — first pack written after the kernel existed rather than alongside it. Four standards (coupling tiers, mutation pattern, version targeting, deployment integrity), one checklist (visibility review), two knowledge files (common pitfalls, API surfaces). |
| pack: ios-swift | 0.2.0 | unchanged |
| adapter: claude-code | 0.2.0 | capability manifest gains a `live-environment` row (⚙️, never agent-established) |
| adapter: codex | 0.2.0 (reviewer + executor slices) | unchanged |
| adapter: gemini | stub | unchanged |

## Release 0.5.0 — 2026-07-25

| Component | Version | Notes |
|---|---|---|
| kernel | 0.4.0 | **contract change** — work-state v2: `plan_hash` → `body_hash` + `approval_code`, approval ladder, approval card, pinned body extraction. Plan template v2. New probe I4. Recompile required. |
| pack: ios-swift | 0.2.0 | unchanged |
| adapter: claude-code | 0.2.0 | compile procedure: `on-plan` carries the approval card and its kernel version |
| adapter: codex | 0.2.0 (reviewer + executor slices) | unchanged; **not yet updated for work-state v2** |
| adapter: gemini | stub | unchanged |

## Release 0.4.0 — 2026-07-12

| Component | Version | Notes |
|---|---|---|
| kernel | 0.3.0 | unchanged |
| pack: ios-swift | 0.2.0 | common-pitfalls v2: + Platform divergence (macOS) section — first knowledge-promotion merge |
| adapter: codex | 0.2.0 (reviewer + executor slices) | + executor.md — orchestrated building-agent role; full adapter still stub |
| adapter: claude-code | 0.2.0 | unchanged |
| adapter: gemini | stub | unchanged |

## Release 0.3.0 — 2026-07-09

| Component | Version | Notes |
|---|---|---|
| kernel | 0.3.0 | + cross-vendor-review workflow, review-packet template |
| pack: ios-swift | 0.1.0 | unchanged |
| adapter: claude-code | 0.2.0 | unchanged |
| adapter: codex | 0.1.0 (reviewer slice) | + reviewer.md — external-reviewer role; full adapter still stub |
| adapter: gemini | stub | unchanged |

## Release 0.2.0 — 2026-07-01

| Component | Version | Notes |
|---|---|---|
| kernel | 0.2.0 | + model-calibration workflow and probe suite (`kernel/calibration/`) |
| pack: ios-swift | 0.1.0 | unchanged |
| adapter: claude-code | 0.2.0 | + dated tier-mapping table |
| adapter: codex | stub | unchanged |
| adapter: gemini | stub | unchanged |

## Release 0.1.0 — 2026-07-01

| Component | Version | Notes |
|---|---|---|
| kernel | 0.1.0 | Initial contracts, principles, personas, standards, workflows, templates, checklists |
| pack: ios-swift | 0.1.0 | SwiftUI-first, Swift concurrency, MV/@Observable default, iOS 26 target / iOS 27 tracking |
| adapter: claude-code | 0.1.0 | Full capability manifest + manual compile procedure |
| adapter: codex | stub | Capability sketch only; not usable |
| adapter: gemini | stub | Capability sketch only; not usable |

Compatibility rule: an adapter at major version N supports kernel major version N. Pre-1.0, treat every minor bump as potentially breaking and read the changelog before updating a pinned project.
