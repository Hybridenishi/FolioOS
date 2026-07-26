# Evidence: survey of `foundryvtt-mcp` as the second governed project

- **Date:** 2026-07-25
- **Subject:** `github.com/Hybridenishi/foundryvtt-mcp` at `213d4c8`
- **Type:** survey — what platform knowledge exists, and where it would live under the
  four-layer architecture

## What the project is

Three deployed components in one repository, not an app:

| Component | Runtime | Source |
|---|---|---|
| MCP server | Node child process over stdio, in the MCP client | `src/` (TypeScript, strict) |
| Sidecar | Express on the Foundry host, Docker | `sidecar/` (plain JS) |
| Bridge module | A Foundry v14 module inside an active GM's browser | `module/` |

Pinned environment: Foundry VTT v14 build 365, `dnd5e` 5.3.3, mixed 2014/2024 rules
content with per-Item provenance preserved.

This shape matters for the pack question. `packs/ios-swift/` describes one artifact built
with one toolchain for one OS. This project is a distributed system whose verification
boundary is a live world with a human-operated browser in it. If the pack abstraction only
fits the first shape, that is a kernel finding, not a Foundry finding.

## Durable knowledge already written down, and where it belongs

The project has already produced pack-grade material. It currently lives in `AGENTS.md`,
`PRIMER.md`, `ROADMAP.md`, and `FINDINGS.md` — read by agents as prose, with no cascade,
no versioning, and no promotion path.

| Existing material | Source | Pack slot |
|---|---|---|
| Tier A/B/C coupling policy — "prefer the layer with the longest maintenance horizon"; Tier C never | `AGENTS.md`, `ROADMAP.md` | `standards/coupling-tiers.md` |
| Mutation pattern: read-only preview → scoped single-use short-lived token → system API → receipt read back from the document | `AGENTS.md`, `sidecar/confirmation.js` | `standards/mutation-pattern.md` |
| Version pinning and the compatibility matrix | `ROADMAP.md` objective, Phase 8 | `standards/version-targeting.md` |
| "Adding a sidecar file requires three edits" (deploy preflight list, Dockerfile `COPY`, scp block) — passes local tests, fails on the host | `PRIMER.md`, commits `4acd4f9`/`213d4c8` | `standards/deployment-integrity.md` |
| v14 requires `extraHeaders: {Cookie}`, not `query: {session}` | `AGENTS.md` rule 1 | `knowledge/common-pitfalls.md` |
| `modifyDocument` delete needs `{ids: [...]}` — not a bare array, not `{_ids: []}` | `PRIMER.md` | same |
| `ChatMessage.type` must be an integer; `author` must be the API user's `_id` | `PRIMER.md` | same |
| AC `formula` is evaluated as dice math — `"leather"` throws `Unresolved StringTerm` | `PRIMER.md` | same |
| Bare `skills.<k>.value` confuses the sheet | `PRIMER.md` | same |
| Exhaustion is a 0–6 level and differs between 2014 and 2024 — read provenance, don't assume | `ROADMAP.md` Phase 2 | same |
| Tier A API surfaces: `Actor.applyDamage`, `Activity#use()`, `Actor#toggleStatusEffect`, `Combat#nextTurn`/`setInitiative`, `pack.getIndex({fields})`, ownership maps | `ROADMAP.md` Phases 2–5 | `knowledge/api-surfaces.md` |
| Journal visibility: required, defaults to GM-only, page ownership explicit, receipts name resolved users not IDs, fail loud on ambiguous names | `ROADMAP.md` Phase 5 | `checklists/visibility-review.md` |

Two of these are not Foundry-specific at all and are candidates for the *kernel*:

- **The receipt rule** — read the changed record back and report before/after values
  before claiming success. That is a general standard for any agent that mutates外部
  external state, and `kernel/standards/code-quality.md` does not currently say it.
- **The deploy-integrity rule** — an artifact's build inputs must be derivable from the
  repository alone, never from files a human left on a host. Generalizes to any deploy.

## Convergent evidence for the approval-code redesign

`sidecar/confirmation.js` is 27 lines and independently arrives at the decomposition that
`.folioos/work/2026-07-approval-ergonomics/plan.md` proposes for plan approval: the
machine issues the token, the caller echoes it back, the binding is checked field-by-field
at consume time, and the token is single-use and short-lived. The human never transcribes
a digest. `consumeConfirmation` distinguishes *expired/absent* from *mismatched binding*
with separate errors — the same distinction the plan draws between a stale stamp and
genuine drift.

`ROADMAP.md` Phase 6 goes further, and is the sharper find: it plans a GM approval queue
for player-initiated writes, "reusing the existing scoped single-use confirmation helper
with the GM as approver," and concludes that **an in-world chat card is the natural
approval surface, since the GM is already looking at Foundry.** That is the same principle
the approval-ladder proposal rests on — put the approval gate where the human already
is — reached independently in a different domain. It is the strongest available argument
that the ladder is a real pattern rather than an accommodation.

## Gaps that block adoption as-is

1. **No `.folioos/` directory.** No work-state, no decisions, no candidates. Substantial
   architectural reasoning exists but sits in narrative documents; `FINDINGS.md` is doing
   ADR work without ADR structure, and `SPEC.md` is explicitly marked historical — a
   staleness problem the decisions log exists to prevent.
2. **`AGENTS.md` vs. compiled `CLAUDE.md`.** The project has a hand-written 56-line
   `AGENTS.md` that is good and that the adapter's compile step would overwrite with a
   generated 150-line `CLAUDE.md`. Adoption has to resolve which is the build output.
   `AGENTS.md` is also the file Codex reads, and the adapter table has no `AGENTS.md` row.
3. **Capability vocabulary has no rung for this project's verification boundary.**
   `run-app` and `screenshots` assume a launchable local app. Here, verifying a write means
   a deployed sidecar plus a hard-refreshed GM browser session holding a 45-second bridge
   token — `npm run smoke:foundry -- --require-bridge`. No capability in
   `kernel/contract/capability-model.md` describes "a live external environment I cannot
   stand up myself," so any workflow needing it cannot declare a ladder, and rule 1 of the
   capability model (every floor requires only `file-system`) is not satisfiable for QA
   here.
4. **Personas are kernel-owned.** `kernel/personas/` holds `staff-ios-engineer` and
   `hig-expert`; there is no mechanism for a pack to contribute a persona, so a
   "Foundry/dnd5e systems engineer" reviewer has nowhere to live except a project override.
5. **Testing policy has no row for an unmockable boundary.** `foundryvtt-mcp` tests pure
   helpers with `node --test` and everything else against a live world. The policy's
   "logic-bearing code → unit tests required" row is satisfiable; the transport and bridge
   layers are the untested part the project's own Phase 1 calls out, and the policy offers
   no bar for them.

Items 3, 4, and 5 are kernel findings surfaced by a second pack. They are the reason this
work is worth doing beyond Foundry itself: one pack cannot distinguish "the kernel is
general" from "the kernel is shaped like the only pack."
