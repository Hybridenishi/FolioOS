# Research notes: `intent.md` — should FolioOS have a standing statement of product intent?

**Prompted by:** the `INTENT.md` / intent-driven-development convention circulating through
AI-coding-agent practice during 2026, in two distinct uses — as an app-level guidance file, and
as a per-feature artifact inside the build loop.

**Status:** Research and evaluation. Nothing in FolioOS changed. This is input for a decision;
under the [autonomy model](../../kernel/principles/autonomy-model.md) the changes sketched in
§8 would need a plan and an approval before any of them land.

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

- **Authored by a defined onboarding procedure, not a blank template** — see §5. Intent is drafted
  by an agent and **ratified by a human**; an unratified intent is not binding. Without that, an
  agent that drafts its own constraints has authored nothing.

- **Kept honest by a derived staleness check, not a self-reported date** — see §6. An earlier
  draft of this note proposed a `last_verified` field; that is an assertion, and a rubber-stamped
  re-date is indistinguishable from a real review. The doc-page template's `covers:` mechanism is
  the better precedent, and it generalises.

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

## 5. Onboarding: how intent gets authored

A blank template at adoption time produces one of two failures — an empty file nobody fills, or a
file filled in five minutes with generic marketing prose that then binds nothing. Intent needs a
procedure, and FolioOS already establishes the right one for each of the two adoption paths in
[adopting-folioos.md](../adopting-folioos.md).

### The rule that shapes everything else

**Intent is drafted by an agent and ratified by a human. An unratified intent is not binding.**

This is not ceremony borrowed from the approval gate — it is the same structural problem. Intent is
the document agents are meant to be *constrained by*. An agent that authors its own constraints has
authored nothing; it has written down what it already believed and given it the authority of a
file. That is the self-authorization failure the
[work-state contract](../../kernel/contract/work-state.md) exists to prevent, appearing one layer
up. So intent carries three human-only frontmatter fields, exactly parallel to rule 1:

```yaml
status: ratified        # draft | ratified
ratified_by: nate       # human identifier; never an agent
ratified_on: 2026-09-05
```

And the parallel to the contract's "a plan carrying `body_hash` but no `approval_code` is a draft":
**an intent with `status: draft` may be read for orientation but must not be cited as a constraint,
used to push back on a request, or used to justify a review finding.** Without that clause, an
agent's inferred draft silently becomes the product's charter.

**No hash, and the reason matters.** The work-state digest exists to bind an approval to a specific
body across an *execution window* — the gap between "human said yes" and "agent acts." Intent has
no execution window; it is read continuously, not executed once. Its drift detector is git, where
every change is a reviewable diff. But git only works as a detector under one rule: **an intent
change lands as its own commit, never bundled into a feature commit** — a two-line loosening of a
non-goal inside a forty-file PR is invisible in review. That rule is intent's analogue of "evidence
is append-only."

### Path A — new project

There is no code to read, so the content can only come from the human. The agent runs a short
intake interview rather than handing over a template: the five headings become five questions, and
the agent writes the answers up as the draft. FolioOS already uses this shape — the PRD's
`## Open product questions` and the plan's `## Risks & open questions` both exist so the agent
surfaces what only the human can settle.

Two rules keep the interview honest: anything the human declines to answer becomes an entry under
`## Open product questions` rather than being invented, and the agent must push for at least one
concrete **non-goal** before ratification. A charter with constraints but no boundaries is the
common failure — non-goals are the half that does the work in §3, and they are the half a human
will skip if not asked directly.

### Path B — existing project

`adopting-folioos.md` already mandates a one-time gap read for brownfield adoption: "where the
codebase already contradicts pack standards, record the *status quo* as ADRs or overrides rather
than pretending." Intent onboarding is the product-side extension of exactly that pass, and reuses
its posture — describe what is actually true, do not pretend.

The agent drafts inferred intent from the README, the existing ADRs, the docs tree, and the shape
of the code, and **marks every line as inferred.** The human's job is then correction, which is far
cheaper than authorship — and a wrong inference is more productive than a blank line, because it
provokes a correction where an empty heading provokes a shrug.

The inference markers are removed at ratification, and any line the human neither confirmed nor
corrected is deleted rather than promoted. Silence is not confirmation.

### Where it sits in the adoption sequence

Currently step 4 of adoption is ADR-0001, the architecture choice. Intent belongs **before** it:
you cannot record why an architecture was chosen without a statement of what is being built. That
reordering is the whole change to the adoption doc — one step inserted, one sentence of rationale.

---

## 6. Keeping it honest: the staleness mechanism

A `last_verified` date — what §4 originally proposed — is an assertion, not a check. It records
that someone claimed to look, and a rubber-stamped re-date is indistinguishable from a real review.
Given that a stale intent file is worse than none (§7), that is not good enough.

FolioOS already solved this problem once, and the solution is better: the
[doc-page template](../../kernel/templates/doc-page.md)'s `covers:` field. Its principle is that
**staleness is derived, not asserted** — "when a covered file changes after `generated:`, the page
is stale by definition." Nobody's judgment is involved in *detecting* staleness; judgment is spent
only on *resolving* it. Intent should work the same way.

### Detection — mechanical

Intent's tripwire is not source files, because intent is not contradicted by an edit. It is
contradicted by a **decision**. So the derived rule is:

> Any ADR in `.folioos/decisions/` dated after `ratified_on`, and any work directory that reached
> `status: done` after `ratified_on`, is an **unreviewed decision** against the current intent.

That is computable from the file system with no judgment at all — the same standard `covers:` sets.
It gives a volume trigger for free, in the shape knowledge-promotion already uses ("monthly, or
when candidates ≥ 5"):

| Trigger | Source |
|---|---|
| **Volume** — N unreviewed decisions since `ratified_on` | mirrors knowledge-promotion's candidate threshold |
| **Time** — release preflight | `release-readiness.md` already carries "docs tree has no stale pages (writer's staleness check run)"; this is the sibling line |
| **Event** — any plan whose `## Re-gate log` cites a product-scope conflict | the re-gate log is already the place scope surprises get recorded |

### Resolution — a review that can fail

A review that asks "is this still accurate?" gets a yes. The
[review pipeline](../../kernel/workflows/review-pipeline.md) already knows this — "findings need a
concrete failure scenario, not a vibe" — and the loop-engineering note in this same directory flags
[cognitive surrender](loop-engineering-vs-agentic-workflows.md) as the specific risk of
rubber-stampable process.

So the review is not a re-read. It is a **diff between stated intent and shipped reality**, and
every question it asks is checkable against artifacts:

1. **Non-goal violations.** For each standing non-goal, does the shipped code now do it? Checkable
   against the tree, not a matter of opinion.
2. **Contradicting decisions.** Which ADRs accepted since `ratified_on` conflict with a stated
   constraint? Either the ADR was wrong or the constraint is dead — both are findings, and both
   need a human.
3. **Silently answered questions.** Which entries under `## Open product questions` were in fact
   settled by something that shipped, and never written back? This is the highest-value check: an
   open question that is no longer open is precisely the authoritative-looking lie.
4. **Load-bearingness.** Did intent visibly shape any plan since the last ratification — a scope
   pushback, a cited non-goal, an intake question? If not, that is itself the finding.

Output is a findings list plus a proposed diff. **The human ratifies; the agent never sets
`ratified_by` / `ratified_on`.** Same gate as knowledge promotion, whose workflow states the reason
plainly: the human is editor-in-chief of their own methodology. They are also editor-in-chief of
their own product.

### Where it lives, and the persona question

This is a **workflow** — `kernel/workflows/intent-review.md` — and a deliberate sibling to
[knowledge-promotion](../../kernel/workflows/knowledge-promotion.md), not a merge with it. §8 keeps
them separate because product learning and methodology learning are a boundary that workflow draws
on purpose; making intent-review its parallel is what that separation implies rather than a
contradiction of it:

| | methodology | product |
|---|---|---|
| capture | `candidates/` | ADRs, re-gate logs |
| periodic review | knowledge-promotion | **intent-review** |
| human gate | promotion | ratification |
| persona | dx-engineer — "reviews *the system*, not the app" | **open question** |

**The persona row is the weakest part of this proposal, and worth flagging rather than papering
over.** The natural owner is the product-manager persona, whose mission ("did we build the thing we
said we'd build") is the right question at the wrong scope. But its evidence inputs are
*deliberately* constrained — the PRD and observable behavior, explicitly "not given the code" — and
checks 1 and 2 above require reading the tree. Widening it damages the decorrelation the review
pipeline is built on ("each persona consumes different inputs, so their blind spots differ").

Three options, none free: widen the PM persona for this pass only (cheapest, some decorrelation
cost, and the exception has to be written down or it will leak into feature reviews); add a persona
(clean, but a whole persona for one periodic workflow is heavy); or run the workflow with no
persona at reasoning tier, with the criteria carried by the workflow document itself. The third is
probably right for a first version — knowledge-promotion names a persona but the *criteria* live in
the workflow, so the persona is doing less work there than it appears.

### The self-limiting clause

`cross-vendor-review.md` builds in its own adoption experiment: "after ~5 runs, if no delta ever
changed a decision, record a candidate and stop — process must earn its maintenance." Intent-review
should carry the identical clause, and it is the direct answer to the ceremony objection in §7: if
five reviews produce no finding that changes intent or catches a violation, the intent file is
decorative, and the honest response is to record a candidate and stop reviewing it — not to keep
re-dating it.

---

## 7. Arguments against, stated fairly

- **A document nobody rereads is ceremony — and a stale one is worse.** This was the strongest
  objection, and §6 is the answer to it: staleness is *derived* from decisions landed since
  `ratified_on` rather than self-reported, the review asks four checkable questions rather than
  "is this still accurate?", and the self-limiting clause retires the whole mechanism if five
  reviews produce nothing. That is a real forcing function, not a date field. What it does not fix
  is a human who ratifies without reading — no artifact can — which is why ratification is a typed
  human act on a diff rather than a checkbox.

- **Single-project evidence.** The same caution the 0.6.0 changelog applied to `packs/foundry-vtt`
  — "one consumer is thin evidence" — applies here. The gap is argued from the shape of the
  contracts, not from a recorded incident where a missing intent statement cost something. That is
  a weaker basis than the approval-ergonomics work, which had four evidence files behind it.

- **Partial overlap with the PRD.** Per-feature product intent already exists. A project-level file
  will drift toward restating it unless the "no feature detail" rule is enforced.

- **It adds an artifact to a system whose stated virtue is that everything is hand-operable.**
  Every new required file is maintenance a human eventually skips. The counter is that this is one
  short file per project, authored once at adoption and touched when the product's boundary moves.

- **§5 and §6 are themselves weight, and this is the new strongest objection.** The original
  proposal was one file and a checklist line. It is now a file, a template, an onboarding
  procedure with two paths, a periodic workflow, three trigger conditions, and an unresolved
  persona question. Each addition is individually justified — the file does not bind without
  ratification, and ratification does not stay true without review — but the honest reading is that
  a project-level intent statement is a *heavier* concept than it first appears, and that weight
  should be priced before adopting rather than discovered afterward. It is a direct argument for
  running the cheap `prd.md` experiment in §9 first.

- **Risk of the wrong fix.** If the real problem is "agents lack product context," a second
  candidate cause is that project PRDs are per-feature by convention rather than necessity. It is
  worth checking whether a *project-level* `prd.md` would close the same gap with no new concept —
  see §9.

---

## 8. What would change, if adopted

Every touched document, with its version cost:

| Document | Change | Cost |
|---|---|---|
| `kernel/templates/intent.md` | **New.** The template projects copy, carrying the three human-only frontmatter fields (`status` / `ratified_by` / `ratified_on`) and the not-binding-while-draft rule. | kernel **minor** |
| `kernel/workflows/feature-lifecycle.md` | Stage 1 (Intake) gains: read intent; if the request contradicts a standing non-goal, surface it *before* planning. | kernel minor |
| `kernel/templates/plan.md` | `## Goal` gains an intent reference; `## Out of scope` may cite standing non-goals instead of re-deriving them. | kernel minor (template v3) |
| `kernel/templates/prd.md` | `## Problem` traces to an intent line. | kernel minor |
| `kernel/personas/product-manager.md` | Intent added to the evidence-input list; "scope creep" widens from *more than this plan asked for* to include *outside what this product is*. | kernel minor |
| `kernel/templates/review-packet.md` | Intent becomes a required packet input (self-containment already implies it). | kernel minor |
| `kernel/checklists/pre-merge.md` | One line under Evidence & docs: if this change moved the product's boundary, the intent change lands as **its own commit** (§5) and re-ratification is due. | kernel minor |
| `kernel/workflows/intent-review.md` | **New** (§6). Periodic product-side sibling to knowledge-promotion: derived staleness detection, four checkable findings, human ratification, self-limiting after ~5 empty runs. | kernel minor |
| `kernel/checklists/release-readiness.md` | One line beside the existing writer's staleness check: intent reviewed if unreviewed decisions have accumulated since `ratified_on`. | kernel minor |
| `adapters/*/compile.md` | One pointer-block line; `on-plan` artifact references `.folioos/intent.md`. ~1–2 lines of the 150-line budget. | adapter patch |
| `userland/project-template/.folioos/intent.md` | Ships with the template, unfilled. | — |
| `docs/adopting-folioos.md` | New step **before** ADR-0001 (you cannot record why an architecture was chosen without knowing what is being built), carrying both onboarding paths from §5 — interview for greenfield, inferred draft for brownfield. | docs |

**Total: still one kernel minor bump — two new documents and eight edits, no contract change and no
recompile-forcing break.** The onboarding and review machinery of §5–6 adds surface without adding
version cost, because a new workflow and a new template are both minor-bump changes under the
[semver definition](../../README.md). That is the main reason this shape is worth preferring over
the more ambitious versions below.

### Deliberately *not* changed

- **`kernel/contract/work-state.md` rule 3 (the re-gate rule).** The natural extension is a new
  trigger: *execution reveals the work contradicts project intent*. It is a genuinely good idea and
  it is a **contract change to an `overridable: false` document — kernel major**, forcing every
  governed project to recompile. Not worth it on the current evidence. Put the same instruction in
  the plan template and the working agreement (both overridable, both minor) and see whether it
  ever fires. If it fires repeatedly, that is the demonstrated need that earns the contract change.

- **`kernel/workflows/knowledge-promotion.md`.** Unchanged, and deliberately so. Intent drift is a
  *product* learning; the candidates loop is explicitly about methodology. §6 makes `intent-review`
  its **sibling** rather than merging the two — same shape (periodic, agent-drafted findings, human
  gate), different subject. Merging them would blur a boundary that workflow draws on purpose.

Note the tie-in to the existing
[loop-engineering research](loop-engineering-vs-agentic-workflows.md): its "engineers are cheapest
at the two constraint points — planning at the start, review at the end" is precisely where the two
lightest insertion points above sit. That is a mild independent corroboration that intake and
review are the right places to spend this, rather than sprinkling intent references through the
build stages.

---

## 9. Alternatives considered

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

  **Run on `foundryvtt-mcp`, 2026-09-05 — answered and closed. It does not close the gap.**
  2 of 6 template sections fit, 1 partially, 2 badly, 1 is dead, and the section carrying the most
  weight — standing constraints — is missing from the template entirely (a constraint is not a
  non-goal). The run also produced a reframe that revises §3: the intent was *already articulated*,
  spread across three documents with three different lifecycles, so the problem is consolidation
  rather than authoring. Full result:
  [intent-prd-experiment-foundryvtt-mcp.md](intent-prd-experiment-foundryvtt-mcp.md).

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

## 10. What this does not settle

- Whether the gap has actually cost anything yet. No incident is on record; the argument is
  structural. Worth a deliberate look back at the `Folio` and `foundryvtt-mcp` work directories for
  a plan that would have been shaped differently by a standing intent file — that check would
  convert this from a plausible gap into an evidenced one. *(Partially advanced: the
  `foundryvtt-mcp` PRD experiment evidenced the template misfit and the scattering, but still not
  an incident where the absence cost something. `Folio` remains unexamined and is the
  differently-shaped second run.)*
- Whether one file is the right granularity for a project with several distinct surfaces
  (`foundryvtt-mcp` is a server, a sidecar and a browser module).
- Whether intent should eventually be a first-class `type:` — deferred to a demonstrated need, per
  ADR-0002.
- **Who owns the intent review** (§6). Widening the product-manager persona costs decorrelation;
  adding a persona is heavy for one periodic workflow; running it persona-less is probably right
  for a first version but is the least-examined of the three. This is the weakest link in the
  proposal and should be settled in the plan, not inherited from this note.
- What N should be for the volume trigger. Knowledge-promotion's "≥ 5" is a precedent, not a
  measurement, and intent accumulates unreviewed decisions at a different rate than candidates.

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
