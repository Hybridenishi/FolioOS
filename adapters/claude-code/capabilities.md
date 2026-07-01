# Adapter: claude-code — Capability Manifest

Adapter version: 0.1.0 · Targets: Claude Code (CLI / desktop / IDE)

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

⚙️ = per-machine configuration; the compile procedure treats unconfigured MCP capabilities as absent and compiles the fallback rungs.

Every workflow's **preferred** rung is available on a fully configured Claude Code — this adapter is the reference implementation.
