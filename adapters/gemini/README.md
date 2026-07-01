# Adapter: gemini — STUB

Status: **not usable.** Seam only; build it when a Gemini-family agent is adopted.

## Capability sketch (verify against the tool at build time)
- `file-system`, `shell`, `web`: expected ✅
- `subagents`: verify → determines whether the review pipeline compiles preferred or fallback rung
- `plan-mode`: verify → work-state files carry the contract either way
- `mcp:*`: verify current MCP support

## Compile targets (expected)
- `always` → `GEMINI.md` (same 150-line budget and distillation rules)
- Other scopes → the tool's command/instruction surfaces, per its conventions at build time

Follow the build steps in `adapters/codex/README.md` — the procedure is identical; only the tool differs.
