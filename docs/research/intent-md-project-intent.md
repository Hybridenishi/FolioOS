# Research notes: `intent.md` — should FolioOS have a standing statement of product intent?

**Prompted by:** the `INTENT.md` / intent-driven-development convention circulating through
AI-coding-agent practice during 2026, in two distinct uses — as an app-level guidance file, and
as a per-feature artifact inside the build loop.

**Status:** Research and evaluation. Nothing in FolioOS changed. This is input for a decision;
under the [autonomy model](../../kernel/principles/autonomy-model.md) the changes sketched in
§6 would need a plan and an approval before any of them land.

---

## 1. What "intent.md" actually refers to

The name covers two practices that are worth separating, because FolioOS's answer differs for each.

### Use A — app-level `INTENT.md`

A single markdown file at the repository root holding the product's standing intent: what is
being built, for whom, what "done" means, and under what constraints. The
[IntentDocs open format](https://www.intentdocs.com/intent-md) describes it as a file "any coding
agent can read," writable by hand with no tooling. Anthropic's worked example (relayed via
[Kotadia's governance write-up](https://agenticaiarch.com/what-is-intent-md/)) gives it five parts:
**the problem, the proposed outcome, the affected users and systems, the constraints, and the
open questions.** The governance framing is that those are the three questions an auditor asks —
who asked for this, why, and under which constraints — so the file is already shaped like the
evidence an examiner wants.

Distinct from `AGENTS.md`/`CLAUDE.md`, which carry *how we work*. `INTENT.md` carries
*what we are building and why*. The two are complementary, not competing.

### Use B — per-feature intent inside the loop

[Exadra37's AI Intent-Driven Development](https://github.com/Exadra37/ai-intent-driven-development)
is the most fully specified version. Intents live in `.intents/` with three status subfolders —
`todo/`, `work-in-progress/`, `completed/` — and files named
`<number>_<type>_<domain-resource>_<slug>.md`. Each intent has three mandatory sections:

| Section | Content |
|---|---|
| **WHY** | The rationale for the request |
| **WHAT** | What is wanted — event models, Gherkin, or plain text |
| **HOW** | The step-by-step implementation plan, tasks and subtasks |

with optional CONSTRAINTS, DESIGN DECISIONS, ALTERNATIVES CONSIDERED, ARCHITECTURE and others.
Agents move the file between status folders as work progresses, check git history to confirm task
completion, and **update the intent's instructions when they turn out to be wrong during
implementation.** The surrounding loop is stated as
Intent → Specification → Plan → Implementation → Verification → updated Intent.

---

## 2. What FolioOS already has

Mapping Use B onto the existing contracts, item by item:

| IDD concept | FolioOS equivalent | Which is stronger |
|---|---|---|
| `.intents/<file>.md` | `.folioos/work/<yyyy-mm>-<slug>/plan.md` | Equivalent |
| WHY | plan `## Goal`; PRD `## Problem` | Equivalent |
| WHAT | PRD `## Outcome` + `## Acceptance criteria` | FolioOS — criteria are checkable, and a named persona reviews against them |
| HOW | plan `## Steps` (files, risk, dependencies, model tier) | FolioOS — considerably richer |
| ALTERNATIVES CONSIDERED | plan `## Options considered`; `.folioos/decisions/` | FolioOS — ADRs outlive the feature |
| todo/wip/completed folders | `status:` frontmatter state machine (`draft → approved → executing → done`, plus `blocked`/`abandoned`) | FolioOS — folder-moves encode state in the path, which is git-hostile and breaks inbound links |
| — | the approval gate, `body_hash` + `approval_code` | FolioOS — IDD has no gate at all |
| "update the intent when it is wrong mid-implementation" | **forbidden** by [work-state](../../kernel/contract/work-state.md) rules 5 and 7 | FolioOS, deliberately |

That last row is the important one. IDD treats the intent as a mutable working document the agent
revises as it learns; FolioOS treats a post-approval body edit as something that *voids the
approval* and forces a re-gate. These are not reconcilable, and FolioOS's position is the correct
one for its threat model: an agent that can edit the thing it was authorized against can authorize
itself. The IDD lifecycle is a strictly weaker state machine wearing similar clothes.

**Conclusion on Use B: reject.** Adopting `.intents/` would add a second, weaker work-state
mechanism alongside the existing one. Everything it offers, `work/` already does better.

---

## 3. The gap Use A exposes

Use A is a different matter, and the honest finding is that FolioOS has a real hole here.

Walk the artifacts a project actually carries:

- `.folioos/work/` — per unit of work. Transient, feature-scoped.
- `.folioos/decisions/` — ADRs. Append-only, immutable, one decision each, retrospective.
- `.folioos/candidates/` — learnings awaiting promotion. About *methodology*, not product.
- `.folioos/overrides/` — deltas against kernel/pack standards. About methodology.
- `.folioos/pin.md` — versions.
- compiled `CLAUDE.md` — 150 lines, hard budget, structured as pointer block / autonomy /
  working agreement / standards digest / escalation map. **Zero lines of product context**, by
  design: it is compiled from kernel documents, and every kernel document is methodology.
- `prd.md` — exists, but the [template](../../kernel/templates/prd.md) places it *inside a work
  directory*, beside the plan. There is no project-level PRD.
- ADR-0001 — mandated, but it is the *architecture* choice, not the product charter.

So: an agent can read the entire cascade — kernel, pack, project overrides, compiled artifacts —
and still have no committed answer to *what is this application for, who is it for, what is it
deliberately not, and which constraints must never be violated.* It infers that from the codebase
and from whatever the human said in chat. Chat is exactly the substrate the
[work-state contract](../../kernel/contract/work-state.md) already refuses to trust for approval.

Four places where that hole is load-bearing rather than cosmetic:

1. **Non-goals have no durable home.** The plan template's `## Out of scope` and the PRD's
   `## Non-goals` are both per-feature, so a standing product boundary — *this app never syncs to
   a server*, *no account system*, *offline is the only supported mode* — is re-derived from
   memory at every planning session, or not at all. Scope creep is caught only when the human
   happens to remember the boundary at the approval gate.

2. **The pushback mandate is under-equipped.** The
   [working agreement](../../kernel/principles/working-agreement.md) makes pushback mandatory and
   agreement a non-deliverable. But an agent with only methodology documents can push back on
   *technical* weakness alone. The highest-value pushback available — *this request contradicts a
   boundary the product already committed to* — has no artifact to stand on. FolioOS asks for a
   behaviour it does not equip.

3. **The PM persona reviews against nothing durable.** Its evidence inputs are, verbatim, "the PRD
   (or the plan's goal section if no PRD), and the observable behavior of the built feature." It is
   asked to flag scope creep — but scope is defined by the plan under review, so it can only catch
   *building more than this plan asked for*, never *building something this product should not
   contain*. The persona is scoped to check a feature against its own promises.

4. **Review packets are not as self-contained as they claim.** The
   [review-packet template](../../kernel/templates/review-packet.md) exists because "the external
   reviewer has no session context." A cross-vendor reviewer receives the plan and the diff and no
   statement of what the product is — so it can assess correctness and craft, but not fit.

Point 2 is the strongest argument for acting. The other three are consequences of the same
absence.

---

## 4. Recommended shape, if this is adopted

**Adopt Use A. Reject Use B.** Concretely:

- **One file per project: `.folioos/intent.md`.** Source of truth lives in the governed directory
  beside `decisions/` and `overrides/`, not at the repo root — root files in FolioOS are compiled
  build outputs (`CLAUDE.md`), and a hand-authored source document at root breaks that separation.
  If cross-agent portability at the root ever matters, the adapter compile can emit or link one;
  that is an adapter concern, not a kernel one.

- **Sections, adapted from the Anthropic five-part shape to what FolioOS actually lacks:**
  *Product* (one paragraph: what this is and for whom) · *Standing constraints* (what must always
  hold) · *Standing non-goals* (what this product does not do, and why) · *Success* (how we know it
  is working, at product level, not feature level) · *Open product questions*.

- **Kept deliberately short and feature-free.** Anything true of only one feature belongs in that
  feature's PRD. The PRD template already warns that "a PRD nobody rereads is ceremony"; the same
  failure mode is worse here, because a stale intent file is an authoritative-looking lie rather
  than merely unread.

- **`type: knowledge`, `layer: project`, `scope: on-plan`** — mirroring the frontmatter that
  `candidates/` files already carry. **This deliberately avoids any contract change:** adding a
  `type: intent` to the document schema would be a kernel *major* bump, and
  [ADR-0002](../../.folioos/decisions/0002-document-contract-not-dsl.md) says schema fields are
  added only under demonstrated need. Use the existing type until intent proves load-bearing.

- **Staleness handled by a `last_verified` date in frontmatter plus one pre-merge checklist line.**
  There is direct precedent: the Second-Brain vault already requires `Date` / `Status` /
  `Last verified` on every durable note, for exactly this reason. FolioOS's compile stamp catches
  stale *compiles*; nothing currently catches stale *content*.

### The one piece of friction worth naming up front

`scope` is single-valued in the [document contract](../../kernel/contract/document-contract.md)
(`always | on-plan | on-review | on-release | on-demand`). Intent is genuinely needed at **two**
scopes: planning, where it constrains what gets proposed, and review, where the PM persona and the
review packet need it. It cannot declare both.

`scope: always` would solve it and is the wrong answer — the 150-line `CLAUDE.md` budget is
described as a scarce resource, and product context competing with the autonomy rules is a bad
trade. The cheap resolution is `scope: on-plan` for compilation, and naming intent as a required
input in `review-packet.md` and in the PM persona's evidence list explicitly — review packets are
hand-assembled and self-contained by construction, so they can pull it in by path without the
schema expressing it. That works, but it is a workaround, and if a second document later needs two
scopes, that is the signal that `scope` should become a list (kernel major).

---

## 5. Arguments against, stated fairly

- **A document nobody rereads is ceremony — and a stale one is worse.** This is the strongest
  objection. Product intent changes more slowly than code but faster than architecture, and there
  is no forcing function that reliably catches a drifted intent file. The `last_verified` +
  checklist proposal is a mitigation, not a guarantee.

- **Single-project evidence.** The same caution the 0.6.0 changelog applied to `packs/foundry-vtt`
  — "one consumer is thin evidence" — applies here. The gap is argued from the shape of the
  contracts, not from a recorded incident where a missing intent statement cost something. That is
  a weaker basis than the approval-ergonomics work, which had four evidence files behind it.

- **Partial overlap with the PRD.** Per-feature product intent already exists. A project-level file
  will drift toward restating it unless the "no feature detail" rule is enforced.

- **It adds an artifact to a system whose stated virtue is that everything is hand-operable.**
  Every new required file is maintenance a human eventually skips. The counter is that this is one
  short file per project, authored once at adoption and touched when the product's boundary moves.

- **Risk of the wrong fix.** If the real problem is "agents lack product context," a second
  candidate cause is that project PRDs are per-feature by convention rather than necessity. It is
  worth checking whether a *project-level* `prd.md` would close the same gap with no new concept —
  see §7.

---

## 6. What would change, if adopted

Every touched document, with its version cost:

| Document | Change | Cost |
|---|---|---|
| `kernel/templates/intent.md` | **New.** The template projects copy. | kernel **minor** |
| `kernel/workflows/feature-lifecycle.md` | Stage 1 (Intake) gains: read intent; if the request contradicts a standing non-goal, surface it *before* planning. | kernel minor |
| `kernel/templates/plan.md` | `## Goal` gains an intent reference; `## Out of scope` may cite standing non-goals instead of re-deriving them. | kernel minor (template v3) |
| `kernel/templates/prd.md` | `## Problem` traces to an intent line. | kernel minor |
| `kernel/personas/product-manager.md` | Intent added to the evidence-input list; "scope creep" widens from *more than this plan asked for* to include *outside what this product is*. | kernel minor |
| `kernel/templates/review-packet.md` | Intent becomes a required packet input (self-containment already implies it). | kernel minor |
| `kernel/checklists/pre-merge.md` | One line under Evidence & docs: if this change moved the product's boundary, `intent.md` is updated in the same PR and `last_verified` bumped. | kernel minor |
| `adapters/*/compile.md` | One pointer-block line; `on-plan` artifact references `.folioos/intent.md`. ~1–2 lines of the 150-line budget. | adapter patch |
| `userland/project-template/.folioos/intent.md` | Ships with the template, unfilled. | — |
| `docs/adopting-folioos.md` | New step: fill intent **before** ADR-0001 — you cannot choose an architecture without knowing what you are building. | docs |

**Total: one kernel minor bump. No contract change, no recompile-forcing break** — which is the
main reason this shape is worth preferring over the more ambitious versions below.

### Deliberately *not* changed

- **`kernel/contract/work-state.md` rule 3 (the re-gate rule).** The natural extension is a new
  trigger: *execution reveals the work contradicts project intent*. It is a genuinely good idea and
  it is a **contract change to an `overridable: false` document — kernel major**, forcing every
  governed project to recompile. Not worth it on the current evidence. Put the same instruction in
  the plan template and the working agreement (both overridable, both minor) and see whether it
  ever fires. If it fires repeatedly, that is the demonstrated need that earns the contract change.

- **`kernel/workflows/knowledge-promotion.md`.** Intent drift is a *product* learning; the
  candidates loop is explicitly about methodology. Conflating them would blur a boundary the
  workflow draws on purpose. Out of scope.

Note the tie-in to the existing
[loop-engineering research](loop-engineering-vs-agentic-workflows.md): its "engineers are cheapest
at the two constraint points — planning at the start, review at the end" is precisely where the two
lightest insertion points above sit. That is a mild independent corroboration that intake and
review are the right places to spend this, rather than sprinkling intent references through the
build stages.

---

## 7. Alternatives considered

- **Extend ADR-0001 into a product charter instead of adding a file.** Cheapest option — ADR-0001
  is already mandatory. **Rejected on lifecycle grounds:** ADRs are append-only and immutable by
  construction ("reversals are new ADRs that supersede; history is never edited"). Intent must be
  *mutable* — the current statement of the product's boundary, revised in place. Putting a mutable
  document in an immutable store means reading N superseding ADRs to reconstruct the present, which
  is precisely the failure a standing intent file exists to prevent. Right instinct, wrong store.

- **Add a project-level `prd.md` rather than a new concept.** Reuses an existing template and adds
  no vocabulary. Weaker on two counts: the PRD template is explicitly shaped for "product-shaped
  work — user-visible behavior with choices to make," i.e. a *change*, and its acceptance-criteria
  spine does not fit a standing charter. But it is the cheapest experiment, and running it for one
  project before committing to a kernel change is a defensible sequencing choice.

- **Do nothing; product intent lives in Second-Brain.** The vault already has
  `Development/<Project>/` hubs with Decisions, Rejected-Alternatives, Ideas and History — arguably
  the natural home. **Rejected, but the reasoning matters:** the vault's own `AGENTS.md` sets an
  authority order in which the repository outranks the vault and the vault is explicitly not the
  operational source of truth. More decisively, an agent working in the repo may have no vault
  access at all, and a cross-vendor reviewer certainly does not. The clean division: **the vault
  holds the reasoning and the history; the repo holds the binding constraint.** Intent, as a thing
  agents must obey, has to be in-repo. A vault note linking to it is the right complement.

- **Adopt IDD wholesale (Use B).** Covered in §2. Rejected — duplicate, weaker work-state.

---

## 8. What this does not settle

- Whether the gap has actually cost anything yet. No incident is on record; the argument is
  structural. Worth a deliberate look back at the `Folio` and `foundryvtt-mcp` work directories for
  a plan that would have been shaped differently by a standing intent file — that check would
  convert this from a plausible gap into an evidenced one.
- Whether one file is the right granularity for a project with several distinct surfaces
  (`foundryvtt-mcp` is a server, a sidecar and a browser module).
- Whether intent should eventually be a first-class `type:` — deferred to a demonstrated need, per
  ADR-0002.

---

## Sources

- [INTENT.md — an open format for developer intent (IntentDocs)](https://www.intentdocs.com/intent-md)
- [AI Intent Driven Development — Exadra37 (INTENT_SPECIFICATION.md)](https://github.com/Exadra37/ai-intent-driven-development/blob/main/INTENT_SPECIFICATION.md)
- [What Is intent.md? Treat It as a Regulated Record from Day One — Harish Kotadia](https://agenticaiarch.com/what-is-intent-md/)
- [IntentSpec — Evidence-Backed Intent for AI Coding Agents (Pathmode)](https://pathmode.io/intentspec)
- [Intent Engineering: The Product Layer Before AI Writes Code (Pathmode)](https://pathmode.io/intent-engineering)
- [Intent-Driven Development for AI Coding](https://intent-driven.dev/)
- [Intents — specs.md](https://specs.md/core-concepts/intents)

Two vendor sites (`intentdocs.com`, `intent-driven.dev`) were not reachable from this environment;
their content is summarised from search-result extracts rather than read in full. The IDD
specification and the governance write-up were read directly.
