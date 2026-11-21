# Adapter: t3-code — Orchestrator Slice

T3 Code as a multi-provider orchestration harness supplying FolioOS's `multi-provider` capability across active providers (Claude, Antigravity/Gemini, Codex, DeepSeek, etc.). Like `adapters/codex/reviewer.md` and `adapters/codex/executor.md`, this slice has **no separate compile pipeline** — projects still compile their instructions through their primary adapter (such as `adapters/claude-code/`) and apply `kernel/workflows/routing-discovery.md` to map active T3 providers onto project work shapes.

Slice version: 0.1.0 · Supplies capability: `multi-provider`

## 1. Provider discovery (once per thread)
- Call `orchestrator_capabilities` once per thread before the first `delegate_task` (call again only after an error or a user-requested provider change).
- Route only to providers and models actually present in `orchestrator_capabilities` and allowed by the project's routing override (`.folioos/overrides/`). If a required provider or model is unavailable, stop and report — never silently substitute an unrequested model or fall back to shell CLIs.

## 2. Native subagents vs. `delegate_task`
- **Native subagents** (e.g., Claude `Agent` with `run_in_background: false`, or Codex `spawn_agent`): use only when the child runs on the Lead's own provider, uses an offered model, requires no custom effort/options beyond the Lead's, shares the current checkout, and runs sequentially/read-only.
- **`delegate_task`**: use whenever delegating to another provider's model, passing custom reasoning-effort options (`target.options`), running background work, or isolating parallel code edits via `isolation: "worktree"`. Never create child worktrees manually in the shell or inside `task`.

## 3. Reasoning effort options (`target.options`)
Read exact option names and allowed values from `orchestrator_capabilities` for the chosen `providerInstanceId` and `model`. Common provider option keys in T3 Code:
- **Claude (`claudeAgent`):** `effort` (e.g., `low`, `medium`, `high`, `xhigh`)
- **Antigravity / Gemini (`antigravity`):** `thought_level` (e.g., `low`, `medium`, `high`)
- **Codex (`codex`):** `reasoningEffort` (e.g., `medium`, `high`, `xhigh`)
- **DeepSeek (`acpRegistry_deepseek_harness`):** `reasoning_effort`

Delegated children must never silently self-escalate; hitting complexity beyond the brief is a stop-and-report signal back to the Lead for Two-Strike escalation.

## 4. 4-part self-contained delegation brief
Every `delegate_task` child starts with zero parent conversation history. Always pass all four parts from [kernel/principles/model-routing.md](../../kernel/principles/model-routing.md):
1. **Authorization:** approved plan path + exact `approval_code`, or `"read-only — no edits"`.
2. **Owned scope:** exact files/steps owned and explicit out-of-scope boundaries.
3. **Safety guardrails:** never edit plan approval fields, never push/open PRs/make unapproved outward writes, plus project-specific protections (e.g., throwaway stores/fixtures only).
4. **Verification & output contract:** exact verification command(s) to run and required output format.

## 5. Parallelism vs. shared-state serialization
- Parallelize with `isolation: "worktree"` (or read-only `isolation: "shared"`) **only** along project-declared parallel-safe seams (independent modules, isolated batch files, read-only research, decorrelated `/folio-review` personas).
- Keep **shared-state bottlenecks strictly sequential** on the Lead or one dedicated full-access owner:
  - **Shared machine/device state:** builds (`xcodebuild`, compiler caches), test apps/simulators, T3 Device tools (`device_list`, `device_open`, `device_screenshot`, `agent-device`), physical-device installs, and shared local ports.
  - **Shared canonical data & generated artifacts:** monolithic canonical data files, published registries, lockfiles, and compiled output bundles/ledgers.

## 6. Cross-vendor review and re-checks
- Every new review or re-check is a **new `delegate_task`** (never `t3_thread_send` onto an existing child thread), preserving zero-history independence for [kernel/workflows/cross-vendor-review.md](../../kernel/workflows/cross-vendor-review.md) and Vendor-Split verification.
- Fix small review findings directly on the Lead rather than spinning up a new delegate.

## 7. Lifecycle and mandatory Lead verification pass
- Follow a running delegate's `next` instruction; never poll (`task_status`, `sleep`, loops). Cancel superseded or unwanted runs with `task_cancel` (never `t3_thread_interrupt` on a `childThreadId`).
- Before accepting any delegate's `"complete"` report, run the Lead verification pass: verify `git diff --stat` covers only planned files (including docs/evidence), inspect actual test/lint receipts, and read checkpoint or safety-boundary changes line by line.
