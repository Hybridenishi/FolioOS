# Adapter: codex — STUB (except the reviewer and executor slices)

Status: **full adapter not usable; two slices are live** — the [reviewer slice](reviewer.md) (0.1.0, external reviewer for cross-vendor-review) and the [executor slice](executor.md) (0.1.0, orchestrated building agent driven by a plan's `## Executor brief`). Neither needs a compile pipeline: the reviewer consumes self-contained packets; the executor consumes the plan itself. The rest of this stub becomes real if/when Codex is adopted as a *standalone* building agent with its own compiled instructions.

## Capability sketch (verify against the tool at build time)
- `file-system`, `shell`: expected ✅
- `subagents`: historically ❌ → the review pipeline compiles its **fallback** rung (sequential persona passes with findings emitted between hats)
- `plan-mode`: ❌ → pure work-state-file flow (the contract was designed for exactly this)
- `run-app`/`screenshots`: verify; likely human-assisted → UX review fallback rung
- `mcp:*`: verify current MCP support

## Compile targets (expected)
- `always` → `AGENTS.md` (same 150-line budget and distillation rules as the claude-code adapter — the budget is a kernel-motivated constraint, not a Claude quirk)
- `on-plan` / `on-review` / `on-release` → prompt files in a conventional location the agent is pointed at; personas become sequential prompt sections rather than parallel agents

## To build this adapter
1. Copy `adapters/claude-code/` as the reference.
2. Write the real capability manifest by testing the tool.
3. Map scopes to the tool's instruction surfaces.
4. Run the same validation + budget rules.
5. Add the adapter to MANIFEST.md with its own version.
