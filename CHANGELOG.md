# FolioOS Changelog

Every entry is a methodology diff: what changed about *how we work*, not just which files moved. Projects read this before adopting a new manifest release.

## 0.6.0 — 2026-07-26

**The second pack.** `packs/foundry-vtt` is the kernel's first pack written after the kernel existed, against `foundryvtt-mcp` — a distributed system (stdio MCP server, Dockerized sidecar, browser-resident module) verified against a human-operated live world rather than an agent-launchable simulator. Drafting it against a differently-shaped project found three places the kernel's contracts had only ever been exercised by `ios-swift`'s shape of verification. See [ADR-0008](.folioos/decisions/0008-second-pack-kernel-boundaries.md).

- **New: pack `foundry-vtt` 0.1.0.** Four standards (coupling tiers, mutation pattern, version targeting, deployment integrity), one checklist (visibility review — the highest-consequence write on `foundryvtt-mcp`'s roadmap, since a wrong hit point is corrected in seconds and a DM note rendered visible to a player cannot be un-seen), two knowledge files (common pitfalls, API surfaces). Content relocated from `foundryvtt-mcp`'s existing docs, not invented.
- **kernel 0.4.0 → 0.5.0 — contract change.** Capability vocabulary gains `live-environment`: an externally-hosted, human-operated instance of the system under test, whose availability the agent cannot establish and must probe. Capability-model rule 1 — "every workflow's floor requires only `file-system`" — gains one narrow, named exception: a workflow requiring `live-environment` may declare a floor that produces a verification artifact (script plus expected receipt) and stops, rather than a file-system-only floor that verifies nothing. Every other capability's rule 1 obligation is unchanged.
- **Document contract clarification.** The cascade already adds "all non-override pack documents (new `id`s)," which includes personas — a pack may contribute one, and the review pipeline picks it up without an adapter change. Stated explicitly rather than left as an inference two adapters could read differently.
- **Testing policy gains a row: external-protocol boundaries.** Transport/bridge layers that cannot be unit-tested without inventing a fake protocol — extract and unit-test the pure decision logic, then require one live smoke check per protocol operation with its receipt recorded as evidence. `foundryvtt-mcp`'s confirmation/actor-utils/bridge-auth modules are the existence proof that the pure core is extractable.
- **`kernel/standards/code-quality.md`** gains one bullet: read the changed record back and report before/after values before claiming success (the receipt rule) — general to any agent mutating external state, promoted rather than left pack-local.
- **claude-code adapter 0.2.0**, unchanged version — capability manifest gains a `live-environment` row (⚙️, never agent-established; workflows requiring it always compile to the artifact-and-stop floor).

**The risk, stated plainly, per the plan's own risk note:** rule 1 exists to stop workflows from being adapter features in disguise. Loosening it is defensible because no file-system-only floor verifies a live Foundry write — but that argument looks the same whether the rule is genuinely wrong or merely inconvenient for this one project. The exception is scoped narrowly (`requires: [live-environment]` only) for exactly this reason; a workflow reaching for it without a genuine unreachable-dependency story is the signal to revisit.

**What this does not settle.** One consumer is thin evidence for a pack — `packs/foundry-vtt` is not yet proven general the way `ios-swift` is. `foundryvtt-mcp`'s own adoption of FolioOS (steps 6–8 of the governing plan) is not yet done; this release covers the kernel- and pack-facing half only.

## 0.5.0 — 2026-07-25

**Approval stops requiring a terminal.** The 64-character hash transcription was not performable from the devices approvals actually happen on, and the workaround — asking an agent to backfill the digest — produced frontmatter byte-identical to a properly approved plan. A degraded path indistinguishable from the real one voids the "the human saw this body" property across the whole log, silently. This release makes the mechanical half mechanical and the human half short enough to type anywhere.

- **kernel 0.3.0 → 0.4.0** — **contract change.** `kernel/contract/work-state.md` (v2) splits `plan_hash` into `body_hash` (full digest, agent-written, explicitly *not* authorization) and `approval_code` (first 8 hex, human-typed, the binding check). The reasoning: drift detection comes entirely from recomputing at execute time, so a mis-presented code fails **closed**; the human's token only needs to be unforgeable by accident. See [ADR-0007](.folioos/decisions/0007-approval-code-countersignature.md).
- **New: the approval ladder.** Three declared rungs — shell (human verifies), editor-only (human types the presented code), chat-only (human replies `approve <slug> <code>`; the agent transcribes **and** files `evidence/<yyyy-mm-dd>-approval.md` with the verbatim message). The floor rung is a knowing, bounded hole in rule 1: it is the rung already in use, and giving it a required artifact strictly increases what a reader can check.
- **New: the approval card.** Agents must present the plan path, digest, and code whenever a plan enters *or re-enters* `draft`. New rule 7 makes a post-approval body edit void the approval explicitly, rather than leaving it as an inference from rule 2.
- **Bug fix in the old rule, independent of the above.** "Content below the frontmatter's closing `---` (exclusive), byte-exact" admitted two faithful readings that hashed the same file to digests sharing no prefix, and a range-based `sed` extraction truncated bodies at their first embedded `---` — which the plan template itself contains. The extraction is now pinned as one normative command whose output *defines* the plan body. The contract's cross-agent portability promise did not previously hold.
- **`kernel/templates/plan.md` (v2)** — new frontmatter fields; a required **"Approving this plan approves:"** block for plans carrying a policy or safety decision; per-step `Depends on:`. Folds in proposal 1 of candidate `2026-07-12-html-approval-views` — the part that needs no HTML and benefits every plan. Proposals 2–3 (derived `plan.view.html`) remain unpromoted.
- **New probe I4** (`kernel/calibration/implementation-04-approval-boundary.md`) — covers the failure mode the split introduces: an agent reconciling a stale `approval_code` against a matching `body_hash` instead of stopping. Sibling to I3; the human's prompt is sincere and mistaken, never adversarial.
- **claude-code adapter compile procedure** — the `on-plan` artifact must carry the approval card verbatim and state the kernel version it was compiled against, so a stale compile is visible at the approval gate rather than at execution.

**Migration.** `plan_hash` is accepted as a deprecated alias for one minor; a plan carrying both schemas is a validation error. Every governed project must recompile its instruction artifacts. Plans approved before 2026-07-25 may show a one-time spurious mismatch from the pinned extraction — re-approve rather than investigate.

## 0.4.0 — 2026-07-12

**First run of the knowledge-promotion loop.** Three project candidates graduated from `Folio:.folioos/candidates/` after triage against the workflow's criteria (durable, general, evidence-backed); archived with outcomes noted.

- **pack ios-swift 0.1.0 → 0.2.0** — `knowledge/common-pitfalls.md` (v2) gains a **Platform divergence (macOS)** section: silent Keychain write failures without a `keychain-access-groups` entitlement, and `navigationDestination` resolution stopping at the column root under `sidebarAdaptable`. Both shipped as real Folio incidents that passed every iOS test.
- **codex adapter 0.1.0 → 0.2.0** — new **executor slice** (`executor.md`): Codex as an orchestrated building agent. Plan-authoring rules for prompt-contract executors (outcome-first steps, in-repo path references only, explicit file/test lists, conflict escape hatch), per-step effort routing with a no-self-escalation rule, and the mandatory orchestrator verification pass ("complete" is a claim to verify — three-for-three calibration lesson). Interface is the plan's `## Executor brief`; no compile pipeline.

Kernel unchanged. Remaining candidates triaged: not-yet-ready ones stay in place with verdicts recorded in the triage notes.

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
