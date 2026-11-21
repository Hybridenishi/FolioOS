---
id: kernel/principles/model-routing
type: principle
layer: kernel
scope: on-plan
requires: []
overridable: true
version: 3
---

# Model Routing

Different work deserves different model tiers, reasoning depths, and specialized providers. Routing is policy, set once and tuned on evidence, so it stays consistent across sessions rather than drifting by habit. A routing decision is a **(tier / work shape, reasoning effort)** pair, not a model name alone. Plans carry a suggested tier or work shape and reasoning effort per step (see [templates/plan.md](../templates/plan.md)).

## Capability tiers and reasoning effort (calibration backbone)

| Tier | Use for | Typical reasoning effort |
|---|---|---|
| **reasoning** | Planning, architecture, tradeoff analysis, invariant-bearing seams, adversarial verification, synthesis, deep debugging | `high`–`xhigh` |
| **implementation** | Writing code against an approved plan, test authoring, structured curation, refactors with clear intent | `medium`–`high` |
| **utility** | Doc regeneration, changelog entries, mechanical renames, formatting, summaries | `low`–`medium` (given precise starting instructions) |

Reasoning depth (`effort`: `low` / `medium` / `high` / `xhigh` / `max`, or the provider's equivalent parameter) is a first-class routing axis that varies independently of model family. A smaller model at high effort and a larger model at low effort are distinct candidates and must be priced and qualified separately.

Any model set (across vendors, open-source, or local runtimes) is placed into these tiers via the [model-calibration workflow](../workflows/model-calibration.md) and its probe suite in `kernel/calibration/`. Placements record **(model × harness × effort × config)** and carry a date; ensembles (mixture-of-agents stacks, cascades) calibrate as a single unit. Dated adapter tier tables live in each adapter's `capabilities.md`, while project-specific multi-provider work-shape routing tables live in `.folioos/overrides/` via [routing-discovery.md](../workflows/routing-discovery.md). Placement rule: **highest tier whose probes pass at a given `(model × harness × effort)`; route to the cheapest qualifying `(model, effort)` combination for the task.**

## Work-shape routing (extensible starter catalog)

Single-provider projects route primarily by tier and effort. Multi-provider projects extend that backbone by mapping concrete **work shapes** to `(provider, model, effort)` pairs. Treat the catalog below as a **starting vocabulary**: [routing-discovery.md](../workflows/routing-discovery.md) prunes shapes a project never exercises (e.g., native UI in a data pipeline or CLI repo), renames shapes to match the project's modules, and adds domain-specific shapes (e.g., multi-source factual corpus curation).

| Starter work shape | Default tier & effort | Characteristics / gating |
|---|---|---|
| **Lead & governance** | `reasoning` (`high`) | Plain-language communication, planning, approval gates, final synthesis |
| **Wide-context research** | `reasoning` (`medium`–`high`) | Repo-wide impact mapping, spec/ADR/history tracing across many files |
| **High-risk / invariant-bearing** | `reasoning` (`xhigh`) | Schemas, migrations, published ID histories, sync, security boundaries — **Vendor-Split required** |
| **Deterministic implementation & tests** | `implementation` (`medium`–`high`) | Approved steps backed by fast, automated test/lint loops |
| **Visual / multimodal** *(when applicable)* | `implementation`–`reasoning` (`high`) | UI layout, interaction polish, mockups, screenshot/accessibility inspection — **vision capability required** |
| **Deep debugging & flaky tests** | `reasoning` (`xhigh`) | Concurrency races, performance regressions, intermittent test failures |
| **Utility & mechanical chores** | `utility` (`low`–`medium`) | Doc regeneration, changelog/release-notes drafts, mechanical cleanup — precise brief required |

## Capability-gated routing and evidence-backed exclusions

- **Modality and capability gates:** Never route a work shape to a model lacking a required capability (for example, visual inspection or UI polish requires a vision-capable model; web-cited factual research requires verifiable source access).
- **Evidence-backed exclusions:** When real work shows a model or tier repeatedly fails a work shape, record an explicit exclusion in the project's routing override linking the incident artifact in `evidence/` or `candidates/`. **Real-work failure outranks benchmarks and probe passes** — demote or exclude first, re-probe second.

## Routing and orchestration rules

1. **Plans are written at reasoning tier.** The plan is where thinking happens; economizing there is false economy.
2. **Execution defaults to implementation tier**, stepping up to reasoning tier (or higher effort) for any step the plan flags as high-risk or ambiguous.
3. **Utility-tier work needs a precise brief.** Weaker tiers execute explicit instructions; they do not recover from vague ones.
4. **Review pipeline:** persona reviews run at each persona's assigned tier and shape (`technical-writer` at utility tier); adversarial verify and synthesis run at reasoning tier. When `multi-provider` is available, dispatch personas across decorrelated providers per [review-pipeline.md](../workflows/review-pipeline.md).
5. **Vendor-Split on high-risk seams:** Never let the same model family both author and solely review a project-declared high-risk or invariant-bearing change (such as persistence schemas, migrations, published ID registries, sync, or security boundaries).
6. **Two-Strike escalation (no silent self-escalation):** When a step fails or stalls twice at its assigned `(model, effort)`, escalate one tier or effort notch rather than retrying a third time. Delegated workers must not silently self-escalate — hitting complexity beyond the brief is a stop-and-report signal back to the Lead.
7. **4-part self-contained delegation brief:** Every delegated subagent starts with zero parent conversation history and must receive:
   1. **Authorization:** approved plan path plus exact `approval_code`, or `"read-only — no edits"`.
   2. **Owned scope:** exact files or steps owned, plus explicit out-of-scope boundaries.
   3. **Safety guardrails:** FolioOS rules (never edit plan approval fields; never perform unapproved outward writes) and project-specific protections (e.g., throwaway stores or test fixtures only).
   4. **Verification & output contract:** exact verification commands to run and required report format.
8. **Mandatory Lead verification pass:** Treat every delegate's "complete" report as a claim to verify, not a fact. Before accepting delegated work, the Lead checks `git diff --stat` against the planned scope (confirming no out-of-scope edits and no skipped docs, commits, or evidence records) and inspects actual verification output. Checkpoint actions and safety-boundary steps receive line-by-line Lead review.
9. **Parallel-safe vs. shared-state sequential seams:** Parallelize only across genuinely independent seams in isolated checkouts (independent modules, isolated unit-test suites, read-only research, isolated batch outputs, decorrelated review personas). Keep **shared-state bottlenecks strictly sequential** on one owner — covering both **shared machine/device state** (builds, simulators/emulators, UI test runners, physical devices) and **shared canonical data or generated artifacts** (monolithic data files, registries, lockfiles, compiled bundles/ledgers).
