---
id: work/2026-11-multi-provider-routing
type: plan
status: done
created: 2026-11-20
body_hash: "sha256:62fc1a6519b37d5fdb88e4284fc15f20c0d99d7c5d3697089e3567c30a0f6fc4"
approved_by: nate
approved_at: 2026-11-20
approval_code: 62fc1a65
---

# Upgrade FolioOS for Multi-Provider Model Routing and Orchestration

## Plain-language summary

**What this is:** Folio 2's recent prototype (PR #40) worked out how to split work across multiple AI providers inside T3 Code (Claude, Gemini/Antigravity, Codex, DeepSeek) — routing research, architecture, UI, debugging, reviews, and chores to different models, enforcing cross-vendor review on high-risk work, and tracking who authored each commit. Right now, all of that lives only in Folio 2's project files, and FolioOS itself still assumes a single-vendor Claude setup.

This plan upgrades FolioOS so multi-provider routing and subagent orchestration are native to the methodology — **without baking Folio 2's iOS rules or T3 Code's tool names into the kernel**, and **without forcing one routing table onto every repo**. It splits the work cleanly across layers:

1. **Kernel (universal rules):** Defines *how* to route by work shape + reasoning effort, when two vendors must check high-risk work (Vendor-Split), when to escalate (Two-Strike), what every delegated subagent brief must include (4-part brief + mandatory Lead verification), how to separate parallel-safe work from shared-state bottlenecks, and honest multi-provider commit authorship.
2. **New Discovery Workflow (`routing-discovery.md`):** A repeatable, hand-operable checklist an agent runs when adopting FolioOS in a project (or when your AI model lineup changes). It inspects that specific repo's structure, tests, risk seams, shared-state bottlenecks, and past incidents, checks which providers/models are actually live, drafts that project's own routing override and commit-authorship ADR, and recompiles the project's instructions within budget.
3. **Adapters (`claude-code` + a new lightweight `t3-code` orchestrator slice):** Keeps T3 Code's specific tool names (`orchestrator_capabilities`, `delegate_task`, worktree isolation) in an adapter slice instead of the kernel, and teaches the Claude Code compile procedure how to compile a project's routing override cleanly without bloating `CLAUDE.md`.

**Decision needed:** Approve this architecture (and the four explicit design calls in "Approving this plan approves" / "Options considered" below) before any kernel, adapter, docs, or manifest files are edited.

**Cost:** Medium documentation/methodology change (~12–14 files in FolioOS, release `0.7.0`). Includes one small contract addition (`multi-provider` capability in `capability-model.md`, which is `overridable: false`). Zero edits to Folio 2 or Concordance v2 in this plan; fully reversible via git.

**If yes:** We create branch `feat/2026-11-multi-provider-routing`, execute steps 1–9, dry-run the cascade and `CLAUDE.md` line budget against both Folio 2 and Concordance v2, and stop before any push or PR. **If no:** Nothing in FolioOS changes; we revise this plan based on your feedback.

## Goal

Make multi-provider model routing, cross-provider subagent orchestration, and project-specific routing discovery first-class in FolioOS (Manifest `0.7.0`, kernel `0.6.0`), while preserving the four-layer boundary (ADR-0001), capability fallback discipline (ADR-0003), files-first operability (ADR-0006), and non-iOS generality validated against both Folio 2 (SwiftUI + CloudKit) and Concordance v2 (Python/JSON character ontology → SQLite ledger + static HTML).

Grounding survey: `evidence/2026-11-20-multi-project-survey.md`.

## Approving this plan approves:

1. **Contract change (`overridable: false`) to `kernel/contract/capability-model.md` (v2 → v3):** adds one capability, `multi-provider` (query available providers/models and dispatch subagents/turns to a chosen provider, model, and effort/options). Capability-model rule 1 is **unchanged** — every workflow using `multi-provider` retains a strict `file-system` floor.
2. **Keeping 3 calibration tiers + adding an `effort` axis + an extensible work-shape starter catalog** in `kernel/principles/model-routing.md` (promoting and closing `.folioos/candidates/2026-07-25-effort-as-routing-axis.md`), rather than replacing the 3-tier calibration model with a rigid 7-slot enum. Also renames `Effort: S | M | L` in `kernel/templates/plan.md` to `Size: S | M | L` so it does not collide with reasoning `effort`.
3. **Keeping `kernel/principles/model-routing.md` at `scope: on-plan` and compiling only a compact routing pointer + hard guardrails (~5–8 short lines) into `CLAUDE.md`**, rather than inlining full provider tables into `scope: always`. Full tables live in `.folioos/overrides/model-routing.md`, `/plan`, and `/folio-review`.
4. **Adding a lightweight orchestrator slice at `adapters/t3-code/orchestrator.md` (`0.1.0`)** — following the `adapters/codex/reviewer.md` and `executor.md` precedent (no separate compile pipeline) — so T3 Code tool names never leak into `kernel/`.
5. **Encoding multi-provider commit authorship as a policy + trailer standard in `kernel/standards/git-workflow.md`** (never hardcode a stale provider `--author` or mutate repo-local `user.*`; credit substantial subagent work via `Co-Authored-By:`; record exact author format in a project ADR) rather than mandating Folio 2's `--author` email literals in every repo.

## Options considered

### A. Capability model: add `multi-provider` vs. overload `subagents`
- **Option A1 — Add `multi-provider` to `kernel/contract/capability-model.md` (Recommended).** Standalone Claude Code has `subagents: ✅` but cannot dispatch to Codex or Gemini. A dedicated capability lets `compile.md` mechanically compile multi-provider rungs when running inside an orchestrator (T3 Code) and clean single-provider fallbacks when standalone, honoring ADR-0003. Narrow scope: do *not* add speculative `vision` or `worktree` capabilities yet.
- **Option A2 — Reuse `subagents` and describe multi-provider dispatch in workflow prose.** Avoids touching an `overridable: false` contract file, but forces runtime improvisation instead of declared capability rungs.
- **Recommendation:** **Option A1.**

### B. Routing abstraction: 3 tiers + effort + extensible work shapes vs. rigid 7-tier replacement
- **Option B1 — Keep 3 calibration tiers (`reasoning`, `implementation`, `utility`) + `effort` axis + extensible work-shape starter catalog (Recommended).** Calibration probes stay tractable (no 7-tier probe explosion); `candidates/2026-07-25-effort-as-routing-axis.md` lands cleanly; projects prune irrelevant shapes (Concordance drops native UI) and add project-specific shapes (Concordance adds factual corpus curation).
- **Option B2 — Replace the 3 tiers with 7 fixed kernel work shapes.** Overfits to Folio 2, breaks existing `kernel/calibration/` probes, and still misses Concordance's primary factual-curation workload.
- **Recommendation:** **Option B1.**

### C. `CLAUDE.md` budget: compact pointer + guardrails vs. full inline table at `scope: always`
- **Option C1 — Keep `model-routing.md` at `scope: on-plan`; `compile.md` emits a compact 5–8 line routing pointer + critical guardrails in `CLAUDE.md` only when a project carries a routing override (Recommended).** Single-provider repos pay 0 lines; multi-provider repos stay comfortably inside the 150-line budget without stuffing 1,000-character run-on paragraphs onto single lines; agents Read `.folioos/overrides/model-routing.md` before first delegation and get full tables in `/plan` and `/folio-review`.
- **Option C2 — Promote `model-routing.md` to `scope: always` and distill the full matrix into `CLAUDE.md`.** Guarantees the full table is always in context even outside `/plan`, but burns scarce always-loaded budget and encourages line-packing workarounds.
- **Recommendation:** **Option C1.**

### D. Where T3 Code specifics live
- **Option D1 — Lightweight orchestrator slice `adapters/t3-code/orchestrator.md` (Recommended).** Mirrors `adapters/codex/{reviewer,executor}.md`: no compile pipeline, pinned in `MANIFEST.md` at `0.1.0`, holds T3 tool names (`orchestrator_capabilities`, `delegate_task`, `isolation: "worktree"`, device tool serialization, per-provider option names). Keeps `kernel/` and standalone `adapters/claude-code/` clean.
- **Option D2 — Put T3 Code guidance inside `adapters/claude-code/`.** Misplaces a cross-vendor orchestrator inside one vendor's adapter (a Gemini or Codex Lead in T3 Code uses the same T3 tools).
- **Recommendation:** **Option D1.**

## Steps

### 1. Branch and extend the capability contract
- **Files:** `kernel/contract/capability-model.md` (edit, v2 → v3)
- **What & why:** Create branch `feat/2026-11-multi-provider-routing`. Add `multi-provider` to the capability vocabulary: *"Query available providers/models and dispatch subagents or turns to a chosen provider, model, and effort/options (optionally in an isolated workspace)."* Leave Rule 1 and its narrow `live-environment` exception untouched.
- **Depends on:** none
- **Risk:** ⚠️ Contract change (`overridable: false`). Scoped to one narrow, proven capability; every workflow referencing it retains a `file-system`-only floor.
- **Model:** reasoning (@ high) · **Size:** S

### 2. Upgrade `kernel/principles/model-routing.md` and `kernel/templates/plan.md`
- **Files:** `kernel/principles/model-routing.md` (edit, v2 → v3), `kernel/templates/plan.md` (edit, v2 → v3)
- **What & why:**
  - In `model-routing.md` (`scope: on-plan`):
    - Remove the hardcoded `Claude mapping (2026)` column (adapter/project concern).
    - Keep the 3 capability tiers (`reasoning`, `implementation`, `utility`) and add **`effort`** as an independent routing axis (`cheapest qualifying (model, effort)`), folding in `.folioos/candidates/2026-07-25-effort-as-routing-axis.md`.
    - Add an abstract **Work-shape routing** starter catalog (Lead & governance; Wide-context research; High-risk / invariant-bearing; Deterministic implementation & tests; Visual / multimodal [when applicable]; Deep debugging; Utility chores), explicitly marked as prunable and extensible per project.
    - Add **Capability-gated routing & evidence-backed exclusions** (modality/capability requirements such as vision; project exclusions anchored to incident evidence).
    - Add **Multi-provider orchestration rules**:
      1. **Vendor-Split:** on project-declared high-risk / invariant-bearing seams, never let one model family both author and solely review.
      2. **Two-Strike escalation:** after two failures/stalls at an assigned `(model, effort)`, escalate one tier or effort notch rather than retrying a third time; delegated workers must not silently self-escalate.
      3. **4-part self-contained delegation brief:** (1) authorization — approved plan path + `approval_code` (or read-only); (2) exact owned files/steps and out-of-scope boundaries; (3) FolioOS & project safety guardrails (no approval-field edits, no unapproved outward writes, throwaway/sandbox targets only); (4) exact verification command and required output format.
      4. **Mandatory Lead verification pass:** treat any delegate's "complete" as a claim to verify (diff scope, tests, docs, evidence) — promoted from `adapters/codex/executor.md`.
      5. **Parallel-safe vs. shared-state sequential seams:** parallelize independent modules, read-only research, isolated batch outputs, and review personas in isolated checkouts; serialize both **shared machine/device state** (builds, simulators, devices) and **shared canonical data/generated files** (monolithic JSON/DB/lockfiles).
  - In `kernel/templates/plan.md` (v3): update the per-step line from `**Model:** reasoning | implementation | utility · **Effort:** S | M | L` to `**Model:** <tier / work-shape> (@ <reasoning effort>) · **Size:** S | M | L` (removing the `Effort` collision) and add an optional `**Routing notes:**` hint for Vendor-Split or parallel-safe execution.
- **Depends on:** step 1
- **Risk:** none (`scope: on-plan`; does not consume `CLAUDE.md` budget directly)
- **Model:** reasoning (@ high) · **Size:** M

### 3. Add the repeatable `kernel/workflows/routing-discovery.md` workflow
- **Files:** `kernel/workflows/routing-discovery.md` (new, v1, `scope: on-demand`, `requires: []`, `overridable: true`)
- **What & why:** Author a hand-operable, files-first procedure an agent runs when adopting FolioOS or refreshing a project's routing:
  - **Stage 0 — Pre-check:** check `.folioos/overrides/` for any existing override targeting `kernel/principles/model-routing` (update in place rather than creating a duplicate target, which fails document-contract validation).
  - **Stage 1 — Project inspection:** map modules/architecture, verification loops (unit tests, linters, CI, UI/live checks), high-risk / invariant-bearing blast-radius seams (Vendor-Split triggers), modality requirements (visual/multimodal, web sourcing, etc.), parallel-safe seams vs. shared-state sequential bottlenecks (both shared machine state and shared canonical data files), and project incident evidence (`evidence/`, `candidates/`, review docs).
  - **Stage 2 — Provider & model inventory:** query active orchestrator providers/models via `multi-provider` (or read adapter manifests + human-supplied lineup on fallback/floor). Tag each placement's basis honestly: `calibrated` vs. `provisional (observed/lineup — demote on real-work failure)`.
  - **Stage 3 — Draft & apply artifacts:** build the project's work-shape table (pruning unused starter shapes, adding domain-specific shapes like factual corpus curation), encode evidence-backed exclusions, map review personas to decorrelated providers, check or draft the project's commit-authorship ADR, write/update `.folioos/overrides/model-routing.md` (valid frontmatter + rationale), and recompile affected adapter artifacts within budget.
  - **Stage 4 — Validate & record:** run document-contract checklist, `CLAUDE.md` ≤ 150 lines, approval-card preservation in `/plan`, and commit-authorship alignment.
  - **Re-run triggers:** active provider/model lineup changes, new major subsystem or invariant-bearing seam, repeated Two-Strike misrouting on a work shape, or switching orchestrator harness.
  - **Capability ladder:** Preferred (`multi-provider`, `shell`) → Fallback (`shell`) → Floor (`file-system`, human supplies provider/model list and runs validation commands).
- **Depends on:** steps 1–2
- **Risk:** none
- **Model:** reasoning (@ high) · **Size:** M

### 4. Upgrade `review-pipeline.md`, `cross-vendor-review.md`, and `model-calibration.md`
- **Files:** `kernel/workflows/review-pipeline.md` (edit, v1 → v2), `kernel/workflows/cross-vendor-review.md` (edit, v1 → v2), `kernel/workflows/model-calibration.md` (edit, v1 → v2)
- **What & why:**
  - `review-pipeline.md`:
    - Generalize Stage 1 persona selection so it no longer hardcodes `staff-ios-engineer` / `hig-expert` (select active pack/project domain engineer + `software-architect`; conditional UI/design persona when user-facing UI changed; `product-manager`; `qa-lead`; `technical-writer` at utility tier).
    - Add multi-vendor Stage 2 dispatch rules: when `multi-provider` is available, route primary code reviewer to a different vendor than the diff author (Vendor-Split), enforce modality gates (vision for visual review), require 4-part briefs with strict evidence isolation, and serialize shared-state evidence capture (screenshots/live runs) before launching parallel reviewers.
    - Clarify Stage 3 (adversarial verify) and Stage 4 (synthesis), plus when formal `cross-vendor-review.md` still applies on top of multi-vendor Stage 2 (checkpoint-class unanchored packet).
    - Update capability ladder: `Preferred (multi-provider)` → `Standard (subagents)` → `Fallback (file-system, shell)` → `Floor (file-system)`.
  - `cross-vendor-review.md`:
    - Remove Swift-specific wording in Prerequisite ("Projects using specialized ecosystems should sanity-check domain reading fluency").
    - Clarify calibration prerequisite honestly: formal R3 verification is the target gate before trusting a model as sole cross-vendor reviewer on checkpoint-class work; provisional live providers are flagged as such in the report header.
    - Clarify distinction from routine multi-vendor `/folio-review`: checkpoint-class work still requires an unanchored, zero-parent-history packet/fixed-prompt review and verbatim append-only `evidence/<date>-crossvendor-<vendor>.md`.
    - Update capability ladder: `Preferred (multi-provider)` → `Standard (shell)` → `Floor (file-system)`.
  - `model-calibration.md`:
    - Require recording `effort` in every placement `(model × harness × effort/config)` and state that probe passes are scoped to the effort tested (resolving candidate `2026-07-25-effort-as-routing-axis.md`).
    - Replace "Swift fluency" with "domain/language fluency".
    - Cross-link `routing-discovery.md` to clarify global capability calibration vs. per-project routing discovery.
- **Depends on:** steps 1–3
- **Risk:** none
- **Model:** reasoning (@ high) · **Size:** M

### 5. Add multi-provider authorship policy to `kernel/standards/git-workflow.md`
- **Files:** `kernel/standards/git-workflow.md` (edit, v1 → v2)
- **What & why:** Add one concise bullet (`scope: always` — tight for the 150-line `CLAUDE.md` budget): in multi-provider projects, commit authorship follows the project's authorship ADR (never hardcode a stale single-provider `--author` or mutate repo-local `user.*`, which misattributes human commits), and substantial delegated subagent contributions are credited with `Co-Authored-By:` trailers. Projects choose their exact identity format (e.g., per-provider `--author` + human committer, or human-authored with trailers) in a project ADR.
- **Depends on:** step 2
- **Risk:** ⚠️ `scope: always` — competes for the 150-line `CLAUDE.md` budget. Kept to a single bullet (~1–2 lines compiled).
- **Model:** implementation (@ medium) · **Size:** S

### 6. Update `adapters/claude-code/` and add `adapters/t3-code/orchestrator.md`
- **Files:** `adapters/claude-code/capabilities.md` (edit), `adapters/claude-code/compile.md` (edit), `adapters/claude-code/VERSION` (edit, `0.2.0` → `0.3.0`), `adapters/t3-code/orchestrator.md` (new), `adapters/t3-code/VERSION` (new, `0.1.0`)
- **What & why:**
  - `adapters/claude-code/capabilities.md`: add `multi-provider` row (`⚙️` — present inside a multi-provider orchestrator such as T3 Code; absent standalone); update the tier-mapping section to note `(model, effort)` placements and remove stale single-effort framing.
  - `adapters/claude-code/compile.md`:
    - Specify how a project's `model-routing` override compiles: (a) compact 5–8 line pointer + critical guardrails in `CLAUDE.md` (never bloat or pack mega-lines; full table stays in `.folioos/overrides/`); (b) work-shape/effort tagging + Vendor-Split rules in `.claude/commands/plan.md`; (c) multi-vendor persona dispatch (or single-provider fallback if `multi-provider` is absent) in `.claude/commands/folio-review.md`; (d) tier-appropriate `model:` frontmatter per persona in `.claude/agents/*.md` (e.g., `technical-writer` → utility/`haiku`).
    - Generalize the Standards digest budget example from `swiftui, concurrency, architecture` to `<active pack standards>`.
  - `adapters/t3-code/orchestrator.md`: lightweight orchestrator slice (0.1.0, no compile pipeline) mapping FolioOS's `multi-provider` capability onto T3 Code: call `orchestrator_capabilities` once per thread; native subagent vs. `delegate_task` boundary; `isolation: "worktree"` for parallel-safe seams; sequentialization of shared-state tools (`device_open`/`device_screenshot`, builds, shared canonical data writes); zero-parent-history 4-part brief contract; provider option keys (`effort`, `thought_level`, `reasoningEffort`); and Lead verification pass.
- **Depends on:** steps 1–5
- **Risk:** none
- **Model:** implementation (@ high) · **Size:** M

### 7. Hook discovery into `docs/adopting-folioos.md` and refresh `userland/project-template/`
- **Files:** `docs/adopting-folioos.md` (edit), `userland/project-template/.folioos/pin.md` (edit)
- **What & why:**
  - Add a **Routing discovery (multi-provider projects)** step to `docs/adopting-folioos.md` under New Project, Existing Project (including scanning existing incident evidence and commit-authorship conventions), and Updating a Project (re-run `routing-discovery.md` when provider lineup or major subsystems change).
  - Refresh `userland/project-template/.folioos/pin.md` from stale `0.3.0` to `0.7.0`, adding optional lines for active orchestrator slice and active overrides so new projects don't start stale.
- **Depends on:** steps 3, 6
- **Risk:** none
- **Model:** implementation (@ medium) · **Size:** S

### 8. Record ADR-0009, close promoted candidate, bump versions, `MANIFEST.md`, and `CHANGELOG.md`
- **Files:** `.folioos/decisions/0009-multi-provider-routing-and-orchestration.md` (new), `.folioos/candidates/2026-07-25-effort-as-routing-axis.md` (edit status to `promoted` with target/release), `kernel/VERSION` (edit, `0.5.0` → `0.6.0`), `MANIFEST.md` (edit, add Release `0.7.0`), `CHANGELOG.md` (edit, add `0.7.0` methodology diff)
- **What & why:**
  - Write ADR-0009 documenting the kernel vs. orchestrator-slice vs. project-override boundaries, why `multi-provider` earned a capability slot while `vision`/`worktree` did not, why work shapes extend rather than replace the 3 tiers, and what the Folio 2 + Concordance v2 sanity check revealed.
  - Mark `2026-07-25-effort-as-routing-axis.md` promoted into kernel `0.6.0`.
  - Publish Manifest `0.7.0` (`kernel 0.6.0`, `adapter: claude-code 0.3.0`, `adapter: t3-code 0.1.0 (orchestrator slice)`, existing packs/adapters unchanged) with a clear methodology diff in `CHANGELOG.md`.
- **Depends on:** steps 1–7
- **Risk:** none
- **Model:** reasoning (@ medium) · **Size:** S

### 9. Multi-project validation dry-run & evidence filing
- **Files:** `.folioos/work/2026-11-multi-provider-routing/evidence/<yyyy-mm-dd>-validation.md` (new), `.folioos/work/2026-11-multi-provider-routing/plan.md` (update `status: done` only after all checks pass, keeping body untouched)
- **What & why:** Execute every check in the Test Plan below, including paper dry-runs of `routing-discovery.md` and `compile.md` against both **Folio 2** and **Concordance v2** (recorded in `evidence/` — without modifying either project repo), verify zero iOS/T3 leaks in `kernel/`, and record validation receipts. Commit locally on `feat/2026-11-multi-provider-routing`; do NOT push or open a PR.
- **Depends on:** step 8
- **Risk:** none
- **Model:** implementation (@ medium) · **Size:** S

## Test plan

- **Document-contract validation checklist** (`kernel/contract/document-contract.md`) across all new/edited documents: required frontmatter fields present, `id` matches repo-relative path and is unique, every `requires:` capability exists in `kernel/contract/capability-model.md`, `version` bumped on every meaningfully changed document.
- **Layer-boundary grep check (zero leaks):** grep `kernel/` for `T3 Code`, `delegate_task`, `orchestrator_capabilities`, `Claude mapping`, `claude-opus`, `gemini-`, `gpt-`, `DeepSeek`, `SwiftUI`, `CloudKit`, `xcodebuild`, `FolioKit`, `FolioStore`, `staff-ios-engineer`, `hig-expert` (outside `kernel/personas/` where those two persona files still live until a future pack move). Zero unexpected hits in modified kernel files.
- **Capability-ladder floor check:** verify `routing-discovery.md`, `review-pipeline.md`, and `cross-vendor-review.md` all declare a `Floor (requires: file-system)` rung without invoking the `live-environment` exception.
- **Two-project dry-run (recorded in `evidence/`):**
  1. **Folio 2 (iOS/macOS SwiftUI + CloudKit):** trace `routing-discovery.md` + `compile.md` → confirm Build 65 UI guardrail, `SchemaV1`/CloudKit Vendor-Split, sequential Xcode/Simulator seam, ADR-0031 authorship, and `CLAUDE.md` ≤ 150 lines without run-on line packing.
  2. **Concordance v2 (Python/JSON ontology → SQLite + static HTML):** trace `routing-discovery.md` + `compile.md` → confirm discovery naturally drops native-UI work, adds multi-source factual corpus curation, flags `id_history.json`/`published_ids.json`/SQLite schema v4 as Vendor-Split seams, flags `corpus/*.json` merges and ledger builds as sequential shared-state seams (with parallel `imports/*.json` batch research), respects Concordance's human-commit convention (or proposes an explicit authorship ADR), and stays ≤ 150 lines.
- **Version & manifest consistency:** `kernel/VERSION`, `adapters/claude-code/VERSION`, `adapters/t3-code/VERSION`, `MANIFEST.md`, `CHANGELOG.md`, and `userland/project-template/.folioos/pin.md` all agree on Release `0.7.0`.
- **Clean scope check:** `git status` and `git diff --stat` confirm only FolioOS files named in Steps 1–9 changed; neither Folio 2 nor Concordance v2 was modified.

## Risks & open questions

- **Single-project prototype maturity:** Folio 2's PR #40 was authored today and has not yet accumulated multi-week execution mileage. Mitigated by keeping the kernel rules strictly limited to patterns already proven across Folio 2 (Build 65 PR B UI incident), `adapters/codex/executor.md` (3-for-3 verification lesson), `candidates/2026-07-25-effort-as-routing-axis.md`, and cross-checked against Concordance v2.
- **Pre-existing iOS personas in `kernel/personas/`:** `staff-ios-engineer.md` and `hig-expert.md` currently sit in `kernel/personas/` rather than `packs/ios-swift/personas/`. Moving them would be a breaking persona-ID change for existing pinned projects, so this plan generalizes `review-pipeline.md` without relocating those files. Confirm if you prefer keeping that relocation deferred.
- **Open decision 1 — `CLAUDE.md` scope (Option C1 vs. C2):** Confirm whether you want `CLAUDE.md` to carry a compact 5–8 line pointer + critical guardrails (with full tables in `.folioos/overrides/model-routing.md`, `/plan`, and `/folio-review`), or prefer keeping the full routing matrix inline in `CLAUDE.md`.
- **Open decision 2 — `multi-provider` contract addition (Option A1 vs. A2):** Confirm you are comfortable bumping `kernel/contract/capability-model.md` (v2 → v3) to add `multi-provider`, alongside the new lightweight `adapters/t3-code/orchestrator.md` slice.
- **Open decision 3 — Commit authorship default:** Confirm that `kernel/standards/git-workflow.md` should require an explicit project authorship ADR + `Co-Authored-By:` trailers without mandating Folio 2's per-provider `--author` email format in repos like Concordance v2 that currently use plain human commits.

## Out of scope

- **Editing Folio 2 or Concordance v2.** Adopting Manifest `0.7.0` in Folio 2 (reconciling PR #40's `.folioos/overrides/t3-model-routing.md`) or adopting FolioOS in Concordance v2 are separate per-project plans after `0.7.0` lands.
- **Moving `staff-ios-engineer.md` or `hig-expert.md` out of `kernel/personas/` into `packs/ios-swift/personas/`.** Deferred to avoid a breaking persona-ID migration in this release.
- **Building full `adapters/codex/` or `adapters/gemini/` compile pipelines.** They remain stubs plus slices until adopted as standalone primary compilers.
- **Adding new calibration probes for work shapes or vision.** Existing `kernel/calibration/` probes remain valid for the 3 core tiers (now annotated with `effort`); new probes enter via the normal `candidates/` loop once real failures occur.
- **Pushing branches, opening PRs, or tagging releases.** Outward-facing writes stay gated behind separate approval.

## Re-gate log
