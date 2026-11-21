# Adapter: claude-code — Capability Manifest

Adapter version: 0.3.0 · Targets: Claude Code (CLI / desktop / IDE — standalone or inside a multi-provider orchestrator)

| Capability | Provided | Notes |
|---|---|---|
| `file-system` | ✅ | Read/Write/Edit tools |
| `shell` | ✅ | Bash tool; permission-gated by the user's settings |
| `subagents` | ✅ | Agent tool + custom agents in `.claude/agents/` |
| `multi-provider` | ⚙️ | Present when running inside a multi-provider orchestrator (e.g., T3 Code — see `adapters/t3-code/orchestrator.md`); absent in standalone Claude Code, where multi-provider workflows compile to their `subagents` or `shell` fallback rungs |
| `plan-mode` | ✅ | Native plan mode; per the autonomy model, the work-state file remains the artifact of record |
| `run-tests` | ✅ | Via shell (`xcodebuild test` / `swift test`) |
| `run-app` | ✅ | Simulator via shell (`xcrun simctl`); interactive UI driving is limited — flows may still need human eyes |
| `screenshots` | ✅ | `xcrun simctl io booted screenshot` |
| `web` | ✅ | WebSearch / WebFetch |
| `mcp:github` | ⚙️ | When the GitHub MCP server (or `gh` CLI as functional equivalent) is configured |
| `mcp:notion` | ⚙️ | When the Notion MCP server is connected |
| `mcp:figma` | ⚙️ | When the Figma MCP server is connected |
| `live-environment` | ⚙️ | Never established by the agent itself; ⚙️ marks that a human-operated instance may or may not be reachable at run time, so workflows requiring it always compile to the artifact-and-stop floor (capability-model rule 1) |

⚙️ = per-machine configuration; the compile procedure treats unconfigured MCP capabilities as absent and compiles the fallback rungs.

Every workflow's **preferred** rung is available on a fully configured Claude Code — this adapter is the reference implementation.

## Tier mapping — updated 2026-11-20

Per [kernel/principles/model-routing.md](../../kernel/principles/model-routing.md) (v3) and [kernel/workflows/model-calibration.md](../../kernel/workflows/model-calibration.md) (v2); basis: vendor lineup + sustained real-world usage (re-run the `kernel/calibration/` probe suite at the target effort when formally qualifying a new model generation).

| Tier | Model | Effort | Notes |
|---|---|---|---|
| reasoning | Opus-class (or above) | `high`–`xhigh` | plans, adversarial verify, review synthesis, high-risk invariants, hard debugging |
| implementation | Sonnet-class | `medium`–`high` | code against approved plans, tests, refactors |
| utility | Haiku-class | `low`–`medium` | doc regeneration, release notes, ADR transcription — given precise briefs |

Recalibration triggers: vendor lineup change, harness major version, effort-setting change, or repeated real-work underperformance at a `(tier, effort)` pair (demote first, re-probe second).
