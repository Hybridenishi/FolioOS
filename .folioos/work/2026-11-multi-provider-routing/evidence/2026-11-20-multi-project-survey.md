# Grounding Survey: Multi-Provider Routing Across Two Project Shapes

- **Date:** 2026-11-20
- **Sources inspected:**
  - FolioOS (`master` @ `88be1e7`, manifest 0.6.0, kernel 0.5.0)
  - Folio 2 prototype (branch `chore/2026-11-t3-model-routing` @ `73b1ce4`, unmerged PR #40; `main` @ `049519a`)
  - Concordance v2 (`/Users/nate/Documents/Concordance-v2` @ `c4c164c`)

## 1. Two-project contrast (Folio 2 vs. Concordance v2)

| Dimension | Folio 2 (SwiftUI + CloudKit app) | Concordance v2 (Python/JSON ontology → SQLite ledger + static HTML) | Kernel implication |
|---|---|---|---|
| **Modules / structure** | `FolioKit`, `FolioStore`, `App/Folio`, `DesignSystem/`, XcodeGen (`project.yml`) | `corpus/*.json`, `tools/*.py`, `tests/*.py`, `imports/*`, `artifacts/` (SQLite + static HTML) | Kernel must describe abstract work shapes & seams, never Swift/Xcode targets. |
| **Verification loops** | `swift test`, `xcodebuild`, `FolioUITests`, Simulator screenshots, `devicectl` | `python3 -m unittest discover -s tests`, `tools/build-ledger.py corpus/ --verify`, `tools/lint-corpus.py corpus/ --baseline origin/main`, `tools/audit.py`, `tools/review-server.py` | Discovery inspects CI/test commands generically (`.github/workflows/`, `README`, build configs). |
| **High-risk / invariant-bearing seams (Vendor-Split triggers)** | `SchemaV1`, CloudKit sync, `Reconciler`, `ConflictQueue`, real-library migrations | `corpus/id_history.json`, `corpus/published_ids.json`, SQLite schema v4 (`tools/build-ledger.py`, `tools/id_history.py`), cross-app contract (`docs/folio-integration.md`) | Vendor-Split triggers on project-declared invariant-bearing seams, not hardcoded persistence names. |
| **Dominant recurring work shape** | App/store implementation + SwiftUI views & HIG polish | **Multi-source factual research & structured JSON curation** (two-tier 3-source citation rules, canon/myth/gacha modeling; `imports/*-{claude,codex,deepseek}.json`) + Python tooling | Kernel work shapes must be a starter catalog that project discovery prunes, renames, or extends. |
| **Visual / multimodal needs** | Heavy & continuous (SwiftUI layout, AX5 Dynamic Type, dark mode, VoiceOver, HIG screenshots) | Light & sporadic (only `generate-browser.py`, `generate-profile.py`, `review-server.py` static HTML/a11y/XSS per `docs/viewer-review.md`) | Kernel states **capability-gated routing & evidence-backed exclusions** generally; SwiftUI/UI guardrails live in project overrides. |
| **Evidence-backed exclusions** | Build 65 PR B (`2026-11-04-pr-b-review.md`): no text-only or budget/flash models on SwiftUI/HIG | Provenance & 3-source citation discipline (preventing unverified web claims on character profiles) | Discovery scans `evidence/`, `candidates/`, and project review docs for real incident guardrails. |
| **Parallel-safe seams** | `FolioKit` rules/importers, `FolioStore` tests, read-only research, isolated `/folio-review` personas | Parallel franchise research into separate `imports/<batch>.json` files, unit tests, read-only audits, independent review personas | Kernel defines isolated-checkout parallelism abstractly. |
| **Shared-state sequential bottlenecks** | Shared machine state: `xcodebuild`, Simulator, `FolioUITests`, `devicectl` | Shared canonical repo files & generated builds: `corpus/*.json` merges, `published_ids.json` updates, `build-ledger.py` ledger builds, `artifacts/` HTML regeneration | Sequential seams must explicitly include **both** shared machine/device state **and** shared canonical data/generated files. |
| **Review personas** | `staff-ios-engineer`, `software-architect`, `hig-expert`, `qa-lead`, `product-manager`, `technical-writer` | `software-architect`, `qa-lead`, `technical-writer`, plus a project/pack Python/data-ontology reviewer (no `staff-ios-engineer`, no `hig-expert` except rare HTML viewer reviews) | `review-pipeline.md` Stage 1 must stop hardcoding `staff-ios-engineer` and `hig-expert`. |
| **Commit authorship** | Per-commit provider `--author` + human committer + `Co-Authored-By:` (`ADR-0031`) | Human commits (`Nathaniel Davis` / `Hybridenishi`), with provider provenance recorded in `imports/*-{claude,codex,deepseek}.json` and record `origin` fields | Kernel requires an explicit authorship policy (ADR) + honest attribution, without forcing one `--author` email table on every repo. |

## 2. Pre-existing kernel/adapter gaps observed

1. **`kernel/principles/model-routing.md`** hardcodes a `Claude mapping (2026)` column inside the kernel and omits the `effort` axis already proposed in `.folioos/candidates/2026-07-25-effort-as-routing-axis.md`.
2. **`kernel/contract/capability-model.md`** has `subagents` (single-provider sub-contexts) but no capability distinguishing multi-provider / per-call-options orchestration (`multi-provider`), so adapters cannot mechanically compile multi-provider vs. single-provider rungs.
3. **`kernel/workflows/review-pipeline.md`** Stage 1 hardcodes `staff-ios-engineer` and `hig-expert`, contradicting ADR-0008's pack-contributed personas rule, and assumes all Stage 2 personas share one base model.
4. **`kernel/workflows/model-calibration.md`** (line 17) and **`kernel/workflows/cross-vendor-review.md`** (line 19) reference Swift fluency directly inside kernel workflows.
5. **`kernel/templates/plan.md`** uses `**Effort:** S | M | L` for task size, which collides with model reasoning `effort` (`low` / `medium` / `high` / `xhigh`).
6. **Folio 2 prototype (`CLAUDE.md` lines 67–72)** raised `t3-model-routing.md` to `scope: always` and packed ~500 words onto 6 ultra-long lines to pass `wc -l <= 150`, and omitted the mandatory Lead verification pass already calibrated in `adapters/codex/executor.md`.
7. **`adapters/claude-code/compile.md`** does not specify compiling `model:` frontmatter per persona in `.claude/agents/` or how a `model-routing` override distills cleanly across `CLAUDE.md` (pointer + hard guardrails), `/plan`, and `/folio-review`.
