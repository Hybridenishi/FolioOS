# Adapter: claude-code — Compile Procedure

Turns kernel ⊕ pack ⊕ project overrides into Claude Code artifacts. Manual today (files-first: this whole procedure is doable by hand); tooling may automate it later, but tooling must produce exactly what this document describes.

## Inputs
1. The merged document set, produced by the cascade in [document-contract.md](../../kernel/contract/document-contract.md) (kernel → pack → project overrides).
2. This adapter's [capabilities.md](capabilities.md), adjusted for what's actually configured on this machine (MCP servers, simulators).

## Output mapping (by `scope`)

| scope | Compiles to |
|---|---|
| `always` | `CLAUDE.md` — **distilled**, see budget below |
| `on-plan` | `.claude/commands/plan.md` (a `/plan` command embedding the plan template, model routing, os-targeting callouts, and the work-state rules) |
| `on-review` | `.claude/commands/folio-review.md` (pipeline orchestration) + one agent per persona in `.claude/agents/` (each agent's prompt = its persona file + its checklists, and *only* its evidence inputs) |
| `on-release` | `.claude/commands/release.md` (workflow + both readiness checklists) |
| `on-demand` | Not compiled — referenced by path (`.folioos/` and the FolioOS checkout are the library; agents Read them when a compiled artifact points there) |

## The CLAUDE.md budget: 150 lines, hard

`always`-scoped documents are **distilled, not concatenated**: rules survive, prose justifications compile down to a link to the source document. Structure:

```
1. Pointer block (~10 lines): where FolioOS lives, where .folioos/ is,
   "the work-state contract governs all non-trivial work"
2. Autonomy + work-state core (~25 lines): plan→approve→execute, hash rule,
   checkpoint actions list, re-gate rule
3. Working agreement core (~10 lines): pushback mandate, honesty rules
4. Standards digest (~60 lines): code-quality, git, security, testing,
   swiftui, concurrency, architecture — bullets only, each section ending
   with its source path
5. Escalation map (~15 lines): "planning → /plan · reviewing → /folio-review
   · releasing → /release · learned something durable → candidates/"
```

Over budget → demote or distill harder; never widen the budget. When compiled instructions and a source document disagree, **the source document wins** — recompile.

## Procedure
1. Validate the document set (contract validation checklist).
2. Apply the cascade; log every override applied (target, operation, rationale).
3. Emit CLAUDE.md per the budget structure; count lines; fail if > 150.
4. Emit commands and persona agents; for each document whose `requires:` includes an unavailable capability, compile the declared fallback rung and note it in the artifact header.
5. Stamp every emitted file with a header: manifest release, component versions, compile date — so a stale compile is detectable at a glance.
6. Commit the emitted artifacts to the project (they are build outputs, but committed ones — the project must work for an agent that can't run the compile).
