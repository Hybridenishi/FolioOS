# Adapter: claude-code — Compile Procedure

Turns kernel ⊕ pack ⊕ project overrides into Claude Code artifacts. Manual today (files-first: this whole procedure is doable by hand); tooling may automate it later, but tooling must produce exactly what this document describes.

## Inputs
1. The merged document set, produced by the cascade in [document-contract.md](../../kernel/contract/document-contract.md) (kernel → pack → project overrides).
2. This adapter's [capabilities.md](capabilities.md), adjusted for what's actually configured on this machine (MCP servers, simulators).

## Output mapping (by `scope`)

| scope | Compiles to |
|---|---|
| `always` | `CLAUDE.md` — **distilled**, see budget below. If a project override targets `kernel/principles/model-routing` (at `scope: always` or `scope: on-plan`), emit **only a compact 5–8 line pointer + critical guardrails** here (telling agents to Read `.folioos/overrides/<routing>.md` before delegating, plus hard exclusions, Vendor-Split triggers, and authorship rules) — never inline full provider routing tables in `CLAUDE.md`. |
| `on-plan` | `.claude/commands/plan.md` (a `/plan` command embedding plan template v3, `(tier / work shape, effort)` routing tables + Vendor-Split triggers, pack callouts, work-state rules, and the verbatim approval-card requirement) |
| `on-review` | `.claude/commands/folio-review.md` (pipeline orchestration compiling `multi-provider` dispatch + Vendor-Split when configured, or the `subagents` fallback rung when standalone) + one agent per persona in `.claude/agents/` (each agent's prompt = its persona file + its checklists, *only* its evidence inputs, and tier-matched `model:` frontmatter — e.g., `technical-writer` at `haiku`, other default personas at `sonnet` with Lead cross-provider dispatch noted when `multi-provider` is active) |
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
   <active pack standards>, plus compact model-routing pointer & guardrails
   (5–8 lines only when a routing override exists; 0 lines otherwise) —
   bullets only, each section ending with its source path
5. Escalation map (~15 lines): "planning → /plan · reviewing → /folio-review
   · releasing → /release · learned something durable → candidates/"
```

Over budget → demote or distill harder; never widen the budget. Never pack multi-sentence prose or full routing tables onto ultra-long single lines to satisfy `wc -l`. When compiled instructions and a source document disagree, **the source document wins** — recompile.

## Procedure
1. Validate the document set (contract validation checklist).
2. Apply the cascade; log every override applied (target, operation, rationale).
3. Emit `CLAUDE.md` per the budget structure; count lines (`wc -l CLAUDE.md`); fail if > 150 or if routing tables were packed into mega-lines.
4. Emit commands and persona agents; check `multi-provider` and every other `requires:` capability against the machine's configuration, compiling declared fallback rungs (e.g., standalone `subagents` vs. `multi-provider` in `/folio-review`) and setting tier-appropriate `model:` frontmatter per persona in `.claude/agents/*.md`, noting the active rung in the artifact header.
5. Stamp every emitted file with a header: manifest release, component versions, compile date — so a stale compile is detectable at a glance. The `on-plan` artifact additionally states the **kernel version** it was compiled against, in a form the agent can read back at run time (`Compiled against kernel: <version>`).
6. Verify the emitted `on-plan` artifact carries the approval-card block from the [work-state contract](../../kernel/contract/work-state.md) verbatim. The card is what makes the approval code reachable by a human without a shell; an `on-plan` artifact that omits it silently reintroduces the transcription burden the contract removed.
7. Commit the emitted artifacts to the project (they are build outputs, but committed ones — the project must work for an agent that can't run the compile).
