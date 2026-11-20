# ADR-0009: Multi-provider model routing, project discovery, and orchestrator slice boundaries

- **Date:** 2026-11-20
- **Status:** accepted
- **Work:** [.folioos/work/2026-11-multi-provider-routing/](../work/2026-11-multi-provider-routing/plan.md)

## Context

FolioOS's initial `model-routing.md` principle hardcoded a `Claude mapping (2026)` table and assumed single-vendor execution, while `review-pipeline.md` hardcoded `staff-ios-engineer` and `hig-expert` despite ADR-0008 opening pack-contributed personas. Meanwhile:

1. `.folioos/candidates/2026-07-25-effort-as-routing-axis.md` identified that reasoning effort (`low` / `medium` / `high` / `xhigh` / `max`) varies independently of model family and must be calibrated and routed alongside tier.
2. `adapters/codex/executor.md` recorded a three-for-three calibration finding that delegated executors pass tests while leaving commits, docs, or evidence records undone — requiring a mandatory Lead verification pass.
3. Folio 2's prototype (PR #40, `chore/2026-11-t3-model-routing`) demonstrated multi-provider routing inside T3 Code (work-shape tables, UI-model guardrail from Build 65 PR B, Vendor-Split, Two-Strike escalation, 4-part delegation briefs, and ADR-0031 multi-provider authorship), but exposed layer risks if copied verbatim into `kernel/`: T3 Code tool names, SwiftUI/CloudKit specifics, and `scope: always` line packing inside `CLAUDE.md`.
4. Testing the design against a second, radically different project — **Concordance v2** (a Python/JSON character ontology compiling to SQLite + static HTML) — confirmed that dominant work shapes (multi-source factual corpus curation), shared-state bottlenecks (shared canonical `corpus/*.json`, `published_ids.json`, and generated `artifacts/` alongside hardware/simulators), and commit-authorship conventions differ sharply across projects.

## Decision

Keep the kernel vs. orchestrator-slice vs. project-override boundaries clean across five moves (Kernel `0.5.0 → 0.6.0`, Manifest `0.7.0`):

1. **Extend the capability contract with `multi-provider` (`capability-model.md` v2 → v3):**
   Adds one capability — querying live providers/models and dispatching subagents/turns with explicit provider, model, and effort/options (optionally in an isolated workspace). Capability-model rule 1 stays unchanged: every workflow using `multi-provider` retains a `file-system`-only floor. Speculative `vision` or `worktree` capabilities are deliberately omitted until multiple workflows require distinct fallback rungs for them.
2. **Keep 3 calibration tiers + add `effort` axis + extensible work-shape starter catalog (`model-routing.md` v3, `plan.md` v3):**
   Keep `reasoning`, `implementation`, and `utility` as the calibration backbone (closing `candidates/2026-07-25-effort-as-routing-axis.md` as promoted), rename `Effort: S | M | L` in `templates/plan.md` to `Size: S | M | L` to prevent collision with reasoning effort, and define a 7-shape starter catalog that projects prune, rename, and extend. Encode capability gating, evidence-backed exclusions, Vendor-Split, Two-Strike escalation (forbidding silent worker self-escalation), 4-part self-contained briefs, mandatory Lead verification, and both categories of sequential shared-state bottlenecks (machine/device state and canonical data/generated files).
3. **Add `kernel/workflows/routing-discovery.md` (`scope: on-demand`):**
   A repeatable, hand-operable workflow run during adoption and on lineup/subsystem/misrouting triggers. It inspects project modules, verification loops, invariant seams, incident history, shared-state bottlenecks, and live providers (tagging uncalibrated live placements `provisional`), then produces `.folioos/overrides/<routing>.md` and an authorship ADR and triggers recompilation.
4. **Keep `model-routing.md` at `scope: on-plan`; compile compactly into `CLAUDE.md` (`adapters/claude-code` `0.3.0`):**
   `CLAUDE.md` emits only a 5–8 line routing pointer and critical guardrails when a project has a routing override (0 lines for single-provider defaults), keeping full tables in `.folioos/overrides/`, `/plan`, and `/folio-review`, and setting tier-matched `model:` frontmatter in `.claude/agents/*.md`.
5. **Isolate T3 Code specifics in `adapters/t3-code/orchestrator.md` (`0.1.0`) and generalize `git-workflow.md` (v2):**
   T3 tool names (`orchestrator_capabilities`, `delegate_task`, `isolation: "worktree"`, device serialization, provider option keys) stay in the lightweight orchestrator slice rather than `kernel/` or `adapters/claude-code/`. `git-workflow.md` requires an explicit authorship ADR + `Co-Authored-By:` trailers and forbids stale hardcoded `--author` defaults or mutating repo-local `user.*`, without imposing Folio 2's `--author` email format on human-commit repositories like Concordance v2.

## Alternatives considered

- **Overload `subagents` instead of adding `multi-provider`:** Rejected because standalone Claude Code has `subagents` but cannot dispatch across Codex or Gemini; overloading blurs compile-time fallback selection.
- **Replace the 3 calibration tiers with 7 fixed work shapes:** Rejected because it multiplies probe maintenance and fails Concordance v2's primary factual-curation workload.
- **Promote `model-routing` to `scope: always` with full inline tables:** Rejected because it burns `CLAUDE.md`'s 150-line budget and incentivizes mega-line packing.
- **Put T3 Code guidance inside `adapters/claude-code/`:** Rejected because any provider acting as Lead in T3 Code uses the same orchestrator tools.
- **Relocate `staff-ios-engineer.md` and `hig-expert.md` from `kernel/personas/` to `packs/ios-swift/personas/` in this change:** Deferred to avoid breaking persona IDs for existing pinned projects; instead, `review-pipeline.md` (v2) selects active pack/project personas without hardcoding iOS names.

## Consequences

Easier: any project adopting FolioOS gets tailored multi-provider routing, high-risk Vendor-Split, and sequential shared-state protection without inheriting SwiftUI assumptions or bloating `CLAUDE.md`. Harder: projects now run `routing-discovery.md` on adoption and lineup changes, and `provisional` live-lineup placements require honesty until R3/tier probes or real-work mileage verify them.

Revisit when: multiple workflows need dedicated `vision` or `worktree` fallback rungs, or when a major manifest bump schedules the persona-ID migration from `kernel/personas/` to `packs/ios-swift/`.
