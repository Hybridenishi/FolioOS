---
id: kernel/workflows/routing-discovery
type: workflow
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Workflow: Project Routing Discovery

A repeatable, hand-operable procedure that inspects a project's architecture, risk seams, verification loops, and active providers, then generates or updates that project's routing override and commit-authorship policy — so no project inherits another app's routing table.

**Relationship to [model-calibration.md](model-calibration.md):** Calibration tests general capability per `(model × harness × effort × config)` across kernel probes. Routing discovery maps a *specific project's* real work shapes, blast-radius seams, incident history, and sequential bottlenecks onto whichever providers and models are currently live.

## Stages

### 0. Existing-override and authorship pre-check
1. Inspect `.folioos/overrides/` for any existing override targeting `kernel/principles/model-routing`. Under the [document contract](../contract/document-contract.md), two overrides in the project layer targeting the same `id` are a validation error — always update the existing override in place and bump its `version:`, rather than adding a second file.
2. Inspect `.folioos/decisions/` for an existing commit-authorship ADR and review recent `git log` authorship conventions.

### 1. Project inspection
Inspect the repository's structure, build/CI configuration (`.github/workflows/`, manifests, `README`), ADRs, and `.folioos/` history to map:
1. **Modules and pipelines:** Core libraries, persistence/data layers, UI surfaces (if any), CLI/scripts, generated artifacts, and external integrations.
2. **Verification loops:** Exact commands that prove correctness per module (unit tests, linters, schema/ledger validators, UI/browser suites, live smoke checks). Work shapes backed by fast deterministic loops can safely delegate further than unverified shapes.
3. **High-risk / invariant-bearing seams (Vendor-Split triggers):** Files or subsystems where silent errors cause data loss, migration corruption, broken downstream contracts, or security defects (schemas, migrations, published ID registries, sync/reconciliation engines, auth gates).
4. **Modality and domain requirements:** Whether visual/UI inspection is frequent, sporadic, or absent; whether tasks require web-cited factual sourcing, specialized languages, or multimodal inspection.
5. **Parallel-safe seams vs. shared-state sequential bottlenecks:**
   - **Parallel-safe:** Independent modules, isolated test suites, read-only repo surveys, separate batch/import files, and isolated review personas running in separate worktrees/checkouts.
   - **Sequential shared-state bottlenecks:** Both **shared machine/device state** (compilers/build caches, simulators/emulators, UI test runners, physical devices, single local ports) **and** **shared canonical data or generated artifacts** (monolithic JSON/SQLite/CSV sources, published ID registries, lockfiles, compiled HTML/bundle outputs).
6. **Incident evidence and prior exclusions:** Scan `.folioos/work/*/evidence/`, `.folioos/candidates/`, and project review docs for real failures tied to specific model classes or modalities.

### 2. Active provider and model inventory
1. When `multi-provider` is available, query the live orchestrator's active providers, model IDs, and exact reasoning-effort/option keys (following the active orchestrator adapter slice). Otherwise read the active adapter's `capabilities.md` and collect the available lineup from the human.
2. Tag every candidate placement's **evidence basis** honestly:
   - **`calibrated`:** Backed by dated [model-calibration.md](model-calibration.md) probe runs at that exact effort level.
   - **`provisional`:** Placed from vendor lineup or observed project usage without formal probe runs — treat as demote-first on real-work failure, and prioritize running [R3 adversarial-verify](../calibration/reasoning-03-adversarial-verify.md) before trusting a provisional model as sole cross-vendor reviewer on checkpoint-class work.

### 3. Draft and apply project artifacts
1. **Tailor the work-shape table:** Start from the starter catalog in [model-routing.md](../principles/model-routing.md). Prune unused shapes (e.g., drop native UI in a headless data/CLI repo), rename rows to match real project modules, and add domain-specific work shapes (e.g., multi-source factual corpus curation). Assign primary `(provider, model, effort/options)` and fallback `(provider, model, effort/options)` per row.
2. **Encode guardrails and orchestration rules:** Write explicit evidence-backed exclusions (linking the incident path), modality gates, Vendor-Split triggers, parallel-safe seams, sequential shared-state seams, and project-specific safety rules for 4-part delegation briefs (e.g., throwaway store/fixture paths, baseline lint rules).
3. **Map `/folio-review` personas:** Assign every active kernel, pack, and project persona to an evidence-isolated, decorrelated provider — keeping primary domain/code review on a different vendor than the diff author, requiring vision on UI/visual personas, and routing `technical-writer` at utility tier.
4. **Align commit authorship:** Following [git-workflow.md](../standards/git-workflow.md), record or update the project's authorship ADR (for example, per-provider `--author` + human committer, or human-authored commits with `imports/` provenance) and require `Co-Authored-By:` trailers when delegated subagents contribute substantially. Never mutate repo-local `user.*` or keep a stale single-provider `--author` hardcoded across providers.
5. **Write/update override and recompile:** Write `.folioos/overrides/model-routing.md` (or update the existing `model-routing` override in place) with `target: kernel/principles/model-routing`, `operation: extend`, `scope: on-plan` (or `scope: always` only if explicitly justified), and a concrete `rationale`. Then run the active adapter's compile procedure so the always-loaded file gains only a compact routing pointer + critical guardrails strictly within its line budget, while `/plan`, `/folio-review`, persona agents (updating tier-appropriate `model:` frontmatter), and `.folioos/pin.md` carry the full compiled rules.

### 4. Validate
1. Run the [document-contract.md](../contract/document-contract.md) validation checklist (required frontmatter, unique `id`, valid `target`, non-empty `rationale`, no duplicate same-layer target).
2. Confirm the compiled always-loaded file stays strictly within its 150-line hard budget without packing multi-sentence prose onto single lines.
3. Verify the compiled `/plan` artifact preserves its kernel version stamp and verbatim `awk` approval card, and confirm the authorship ADR matches `git-workflow.md`.
4. Record the discovery summary and validation output as evidence if executed under a plan, or commit the adoption/refresh artifacts together.

## When to re-run this workflow
- An active provider, model generation, or orchestrator harness changes.
- The project adds a major new subsystem, pipeline, or invariant-bearing seam.
- Real work triggers Two-Strike escalations or repeated misrouting on a work shape (demote first, then update the override and recompile).

## Capability ladder
- **Preferred** (`requires: [multi-provider, shell]`): Query live providers/models via the orchestrator slice, inspect git/CI/test structure via shell, write/update the override and authorship ADR, recompile, and run validation checks.
- **Fallback** (`requires: [shell]`): Inspect repo via shell, inventory CLI/adapter model configs with the human, draft/update the routing override and authorship ADR, recompile, and validate.
- **Floor** (`requires: [file-system]`): Read project files directly, ask the human for their active provider/model list, draft the routing override, authorship ADR, and compile diff as files for the human to review and validate.
