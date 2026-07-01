---
id: kernel/contract/capability-model
type: contract
layer: kernel
scope: on-demand
requires: []
overridable: false
version: 1
---

# Capability Model

Documents are abstracted by the document contract; **capabilities are abstracted here.** Agents differ in what they can *do* — spawn subagents, run the app, reach MCP servers — and a workflow written against one agent's abilities is unportable. So workflows declare abstract capability requirements and explicit fallback ladders, and each adapter publishes a manifest of what its agent provides.

## Capability vocabulary (v1)

| Capability | Meaning |
|---|---|
| `file-system` | Read/write project files. **The floor — every agent is assumed to have it.** |
| `shell` | Execute build/test/tooling commands |
| `subagents` | Spawn parallel or specialized sub-contexts |
| `plan-mode` | A native plan-then-approve mechanism (absent → use work-state files alone) |
| `run-tests` | Execute the project test suite and read results |
| `run-app` | Launch the app/simulator and observe behavior |
| `screenshots` | Capture UI images for visual review |
| `web` | Search/fetch external documentation |
| `mcp:github` | GitHub via MCP (PRs, issues, reviews) |
| `mcp:notion` | Notion via MCP (doc/decision sync) |
| `mcp:figma` | Figma via MCP (design source of truth) |

Add capabilities by amending this vocabulary (kernel minor bump). Never let a workflow reference a capability that isn't listed here.

## Adapter manifests

Each adapter ships `capabilities.md` declaring which capabilities its agent provides, with any caveats. Compilation checks every document's `requires:` against the manifest.

## Fallback ladders

Every `workflow` document that requires more than `file-system` **must** declare a ladder:

```markdown
## Capability ladder
- **Preferred** (requires: subagents, run-tests): …full behavior…
- **Fallback** (requires: run-tests): …sequential single-context behavior…
- **Floor** (requires: file-system): …checklist-driven manual behavior…
```

Rules:

1. The floor of every workflow requires only `file-system`. If a workflow can't express a file-system-only floor, it isn't a workflow — it's an adapter feature, and belongs in the adapter.
2. Degradation is *declared by the workflow author*, not improvised by the agent at run time. An agent on a rung follows that rung's text.
3. Outward-facing capabilities (`mcp:*`, anything that publishes) are additionally governed by the autonomy model's checkpoint rules — having a capability is not authorization to use it.

## Design rule: capabilities are not integrations

There is no separate "integrations" concept. GitHub, Notion, and Figma are capabilities; documents that use them declare `requires: [mcp:github]` and provide a fallback (usually: produce the artifact locally and let the human publish it).
