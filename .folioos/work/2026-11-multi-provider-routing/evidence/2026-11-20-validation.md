# Validation & Multi-Project Dry-Run Receipt

- **Date:** 2026-11-20
- **Work item:** `work/2026-11-multi-provider-routing` (`approval_code: 62fc1a65`)
- **Branch:** `feat/2026-11-multi-provider-routing`
- **Target release:** FolioOS Manifest `0.7.0` (`kernel 0.6.0`, `adapter: claude-code 0.3.0`, `adapter: t3-code 0.1.0`)

## 1. Automated & structural verification receipts

### Document-contract checklist (`kernel/contract/document-contract.md`)
Verified across all 8 modified/new kernel files:
- `kernel/contract/capability-model.md` (`version: 3`, `overridable: false`, `scope: on-demand`, `requires: []`)
- `kernel/principles/model-routing.md` (`version: 3`, `overridable: true`, `scope: on-plan`, `requires: []`)
- `kernel/templates/plan.md` (`version: 3`, `overridable: true`, `scope: on-plan`, `requires: []`)
- `kernel/workflows/routing-discovery.md` (`version: 1`, `overridable: true`, `scope: on-demand`, `requires: []`)
- `kernel/workflows/review-pipeline.md` (`version: 2`, `overridable: true`, `scope: on-review`, `requires: []`)
- `kernel/workflows/cross-vendor-review.md` (`version: 2`, `overridable: true`, `scope: on-review`, `requires: []`)
- `kernel/workflows/model-calibration.md` (`version: 2`, `overridable: true`, `scope: on-demand`, `requires: []`)
- `kernel/standards/git-workflow.md` (`version: 2`, `overridable: true`, `scope: always`, `requires: []`)

Every `id` matches its repo-relative path without extension and is unique; `requires: []` uses only valid capability vocabulary entries; versions were bumped on every changed file.

### Layer-boundary grep check
Command run across all 8 modified/new kernel files:
```sh
grep -nE 'T3 Code|delegate_task|orchestrator_capabilities|Claude mapping|claude-opus|gemini-|gpt-|DeepSeek|SwiftUI|CloudKit|xcodebuild|FolioKit|FolioStore|staff-ios-engineer|hig-expert|Swift|iOS' \
  kernel/contract/capability-model.md \
  kernel/principles/model-routing.md \
  kernel/templates/plan.md \
  kernel/workflows/routing-discovery.md \
  kernel/workflows/review-pipeline.md \
  kernel/workflows/cross-vendor-review.md \
  kernel/workflows/model-calibration.md \
  kernel/standards/git-workflow.md
```
Result: **0 matches (`ZERO MATCHES (PASS)`).** (`staff-ios-engineer.md` and `hig-expert.md` remain untouched in `kernel/personas/` per the approved plan.)

### Capability-ladder floor check
Verified `Floor` (`requires: [file-system]`) in all three updated/new workflows without invoking `live-environment`:
- `kernel/workflows/routing-discovery.md:61` — `- **Floor** (`requires: [file-system]`): ...`
- `kernel/workflows/review-pipeline.md:46` — `- **Floor** (`requires: [file-system]`): ...`
- `kernel/workflows/cross-vendor-review.md:41` — `- **Floor** (`requires: [file-system]`): ...`

### Version & manifest consistency
- `kernel/VERSION`: `0.6.0`
- `adapters/claude-code/VERSION`: `0.3.0` (`capabilities.md` header `0.3.0`)
- `adapters/t3-code/VERSION`: `0.1.0` (`orchestrator.md` slice version `0.1.0`)
- `MANIFEST.md`: Release `0.7.0 — 2026-11-20` pins `kernel 0.6.0`, `adapter: claude-code 0.3.0`, `adapter: t3-code 0.1.0 (orchestrator slice)`
- `CHANGELOG.md`: `0.7.0 — 2026-11-20` methodology diff recorded
- `userland/project-template/.folioos/pin.md`: Manifest release `0.7.0`, Adapter `claude-code 0.3.0`

### Clean external-repo & scope check
- `/Users/nate/Documents/Folio v2` (main checkout and `chore/2026-11-t3-model-routing` worktree): unmodified.
- `/Users/nate/Documents/Concordance-v2`: unmodified.

---

## 2. Two-project paper dry-run (`routing-discovery.md` + `compile.md`)

Dry-run traced against the actual repositories inspected in `evidence/2026-11-20-multi-project-survey.md` (neither project modified):

### Case A — Folio 2 (SwiftUI + CloudKit multi-platform app)
- **Stage 0 (Pre-check):** Detects existing `.folioos/overrides/t3-model-routing.md` (`target: kernel/principles/model-routing`) and `.folioos/decisions/0031-multi-provider-commit-authorship.md`. Discovery updates `.folioos/overrides/t3-model-routing.md` in place (`version: 1 → 2`) rather than creating a second file targeting `model-routing`.
- **Stage 1 (Inspection):** Identifies `FolioKit`, `FolioStore`, `App/Folio`, `DesignSystem/`; verification loops (`swift test`, `xcodebuild`, `FolioUITests`, Simulator screenshots, `devicectl`); invariant seams (`SchemaV1`, CloudKit sync, `Reconciler`, `ConflictQueue`, real-library migrations); Build 65 PR B incident (`2026-11-04-pr-b-review.md`); parallel-safe seams (`FolioKit`, `FolioStore` tests, read-only research, `/folio-review` personas) vs. sequential shared machine/device state (`xcodebuild`, `FolioUITests`, Simulator/T3 Device tools, `devicectl`).
- **Stage 2–3 (Artifacts):** Retains all 7 starter shapes tailored to Folio 2 modules, tags live lineup placements as `provisional` until formal probes run, enforces the Build 65 UI-model guardrail + vision requirement, enforces Vendor-Split on `SchemaV1`/CloudKit/migration work, requires the Lead verification pass from `model-routing.md` v3, and preserves ADR-0031 authorship + `Co-Authored-By:` trailers.
- **Compile (`adapters/claude-code/compile.md` 0.3.0):** Replaces the 6 ultra-long packed lines in Folio 2's current `CLAUDE.md` (lines 67–72 of 81) with a 6–7 line compact pointer + critical guardrails (Read `.folioos/overrides/t3-model-routing.md`, Build 65 UI guardrail, Vendor-Split, sequential Xcode/Simulator seam, Lead verification, ADR-0031 authorship). Projected `CLAUDE.md` length: **~82 lines** (well under the 150-line hard limit, zero line-packing). `/plan` adopts `Size:` + `Routing notes:`; `/folio-review` compiles `multi-provider` Stage 2 dispatch; `.claude/agents/technical-writer.md` stays `model: haiku`.

### Case B — Concordance v2 (Python/JSON character ontology → SQLite ledger + static HTML)
- **Stage 0–1 (Inspection):** No existing `.folioos/` directory yet. Inspection of `README.md`, `.github/workflows/validate.yml`, `tools/*.py`, `tests/*.py`, `corpus/*.json`, `imports/`, `docs/folio-integration.md`, and `docs/viewer-review.md` maps:
  - Verification loops: `python3 -m unittest discover -s tests`, `tools/build-ledger.py corpus/ --verify`, `tools/lint-corpus.py corpus/ --baseline origin/main`, `tools/audit.py corpus/`.
  - High-risk / invariant seams (Vendor-Split triggers): `corpus/id_history.json`, `corpus/published_ids.json`, SQLite schema v4 (`tools/build-ledger.py`, `tools/id_history.py`), and the cross-app SQLite contract (`docs/folio-integration.md`).
  - Parallel-safe seams: parallel multi-source franchise research writing into isolated `imports/<batch>-<provider>.json` files, unit tests, read-only corpus audits, and review personas.
  - **Sequential shared-state bottlenecks (canonical data / generated files):** merges into `corpus/*.json`, `published_ids.json` updates, `build-ledger.py` ledger builds, and `artifacts/browser.html` / `artifacts/profiles/*.html` regenerations (only one owner writes canonical corpus/ledger artifacts at a time).
- **Stage 2–3 (Artifacts):**
  - Prunes native iOS/SwiftUI shapes; keeps a narrow, occasional web/HTML/a11y shape (`tools/generate-*.py`, `docs/viewer-review.md`); **adds** Concordance's dominant custom shape: **Multi-source factual corpus curation** (enforcing the 3-source / two-tier citation rule, `origin` provenance, and canon/myth/gacha rules).
  - Review personas map to `software-architect`, `qa-lead`, `technical-writer`, plus a project Python/data-ontology reviewer — zero `staff-ios-engineer` or `hig-expert` bloat (rarely invoking a UI/HTML accessibility check only when `generate-*.py` changes).
  - Authorship ADR respects Concordance v2's existing human-commit git history (`Nathaniel Davis` / `Hybridenishi`) paired with provider provenance in `imports/*-{claude,codex,deepseek}.json` and `Co-Authored-By:` trailers when delegated agents author code/tooling.
- **Compile (`adapters/claude-code/compile.md` 0.3.0):** With no `ios-swift` pack standards (~8 lines saved relative to Folio 2) and a 6–7 line routing pointer + guardrails, projected `CLAUDE.md` length is **~74–76 lines** (≤ 150 hard limit, zero SwiftUI/T3 leakage).
