# Adapter: claude-code — Capability Manifest

Adapter version: 0.2.0 · Targets: Claude Code (CLI / desktop / IDE)

| Capability | Provided | Notes |
|---|---|---|
| `file-system` | ✅ | Read/Write/Edit tools |
| `shell` | ✅ | Bash tool; permission-gated by the user's settings |
| `subagents` | ✅ | Agent tool + custom agents in `.claude/agents/` |
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

## Tier mapping — calibrated 2026-07-01

Per [kernel/principles/model-routing.md](../../kernel/principles/model-routing.md); basis: vendor lineup + sustained real-world usage (the probe suite in `kernel/calibration/` postdates this mapping — run it formally when the lineup changes).

| Tier | Model | Notes |
|---|---|---|
| reasoning | Opus-class (or above, e.g. Fable-class where available) | plans, review synthesis, adversarial verify, hard debugging |
| implementation | Sonnet-class | code against approved plans, tests, refactors |
| utility | Haiku-class | doc regeneration, release notes, ADR transcription — given precise briefs |

Recalibration triggers: vendor lineup change, harness major version, or repeated real-work underperformance at a tier (demote first, re-probe second).
