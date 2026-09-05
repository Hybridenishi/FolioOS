# Experiment: does a project-level `prd.md` close the intent gap?

**Subject:** `foundryvtt-mcp` (Hybridenishi/foundryvtt-mcp @ `c535a7e`)
**Artifact:** [`docs/PRD.md`](https://github.com/Hybridenishi/foundryvtt-mcp/pull/13) — draft, unratified
**Run:** 2026-09-05
**Status:** Complete. Result recorded; no kernel change made.

---

## Why this ran

[`intent-md-project-intent.md`](intent-md-project-intent.md) §9 named a cheaper alternative to a
new `intent.md` concept: **use a project-level `prd.md` instead.** It reuses an existing template
and adds no vocabulary. §7's weight objection made running it first the recommended sequencing —
the note argued against its own proposal on the grounds that the machinery in §5–6 is heavier than
it first appears.

The experiment: draft a project-level PRD for one real project using
[`kernel/templates/prd.md`](../../kernel/templates/prd.md) unmodified, and record where it fits and
where it breaks. `foundryvtt-mcp` was chosen because §10 named it as one of the two projects worth
a look-back, and because its shape (server + sidecar + browser module) is the one the kernel has
least experience with.

---

## Method

The brownfield path from §5: the agent drafts inferred intent from existing sources, marks every
line as inferred, and the human corrects. Sources were `docs/ROADMAP.md` ("Objective", phase
goals), `AGENTS.md` (coupling policy, write pattern, secrets), and `README.md`. Nothing was
invented; anything the sources could not settle went to open questions rather than being filled in.

Three drafted constraints were then checked against source to test whether charter claims are
mechanically verifiable — the property §6's derived-staleness design depends on.

---

## Result 1 — template fit

| Template section | Fit | Why |
|---|---|---|
| `## Problem` | **Partial** | Works, but cannot distinguish the *originating* problem (largely solved by the product existing) from the *ongoing* reason to exist. Two different sentences, one heading. |
| `## Outcome` | **Poor** | Defined as "the user-observable difference when this ships." A standing charter never ships. No rewording of the heading fixes the tense. |
| `## Acceptance criteria` | **Poor — the primary failure** | Its spine is per-change verifiable checkboxes that "the PM persona reviews against exactly these." At project scope it degrades into either (a) an enumeration of all 48 tools that duplicates `ROADMAP.md` and rots on the next merge, or (b) product-level statements that are true and durable but not per-criterion verifiable. The template's `empty state` / `error states` prompts are feature-level and have no charter-level meaning at all. |
| `## Non-goals` | **Good** | Carries real charter weight unmodified. The best-fitting section by a wide margin. |
| `## Design notes` | **Dead** | Prompt is "mockups, references, HIG patterns." `foundryvtt-mcp` is a headless MCP server with no interface. Not merely empty — shaped for a different kind of product. |
| `## Open product questions` | **Good** | Works unchanged, and produced three genuine questions the inference could not settle. |
| **Standing constraints** | **Missing entirely** | Not a template section. Had to be invented. |

**2 of 6 fit, 1 partially, 2 fit badly, 1 is dead — and the section carrying the most weight is
absent from the template.**

### The missing section is the finding

Seven of this project's durable intent statements are **constraints**, not non-goals: coupling
tiers, the preview→token→API→receipt mutation pattern, visibility required and defaulting to
GM-only, fail-loud name resolution, the permanently read-only player surface, Foundry-not-the-vault
as index source, secrets handling. A non-goal says *what will not be built*; a constraint says
*what binds the things that are built*. The PRD template has no place for the second, because a
per-feature PRD inherits its constraints from the standards cascade — which is exactly what a
project-level charter cannot do, since the constraints in question are the project's own.

**Verdict on §9's cheap path: it does not close the gap.** Adapting the PRD template to charter
scope means deleting two sections, rewriting two, and adding one it does not have. That is a
different template wearing the PRD's name, and pretending otherwise would leave every future reader
to rediscover the misfit.

---

## Result 2 — the reframe (the more valuable finding)

`foundryvtt-mcp` **already had** substantial standing product intent before this experiment.
`ROADMAP.md`'s "Objective" is a product statement carrying an explicit, load-bearing non-goal:

> This is a personal server for one table. It will not become a system-neutral Foundry integration.

`AGENTS.md` carries the coupling policy and the mutation pattern. `README.md` carries the consumer
boundaries. The intent was articulated, deliberately and well.

**The gap is not authorship. It is that the intent is spread across three documents with three
different lifecycles** — a plan that changes as phases complete, an agent-instruction file that
changes when tooling changes, and a user-facing readme that changes when the interface changes.
None of them is a charter. So no single statement can be cited in a plan, reviewed for staleness,
or ratified, and a reader has to know all three exist to reconstruct what the product is.

This substantially revises the framing in `intent-md-project-intent.md` §3, which argued the gap
from the *absence* of an artifact. On this evidence the problem is **consolidation**, not
authoring — which is a cheaper problem, and changes what the onboarding procedure in §5 is
actually for: the brownfield path is not eliciting new intent, it is gathering scattered intent
into one citable place. That is a better fit for what the agent could actually do here, and it
predicts the greenfield interview path is the harder and less-evidenced of the two.

---

## Result 3 — charter claims are mechanically checkable

Three drafted constraints were verified against source in minutes, and the artifact records their
citations inline:

| Constraint | Verified at |
|---|---|
| Player routes mounted ahead of GM-only auth; all read-only | `sidecar/app.js:437` vs `:439-440` |
| Tests assert known credential strings are absent from source | `sidecar/bridge-auth.test.js:22-23`, `module/scripts/prepared-actor-bridge.test.mjs:8` |
| Hidden and absent are indistinguishable to a player | `sidecar/app.test.js:596` |

This is direct support for §6's design: a constraint stated concretely enough to cite is one a
periodic review can re-check mechanically rather than by re-reading. The converse also held — the
claims that resisted citation were precisely the ones that ended up in open questions. That
suggests a drafting rule worth encoding: **a constraint that cannot be pointed at is an open
question wearing a constraint's clothes.**

---

## What this changes in the recommendation

- **§9's project-level `prd.md` alternative is answered and closed.** It is cheaper, and it does
  not work. Record the outcome rather than leaving it as an untested option.
- **§3's framing needs revising** from "no artifact states product intent" to "product intent
  exists but is scattered across documents with incompatible lifecycles." The consequences listed
  in §3 (non-goals with no durable home, an under-equipped pushback mandate, a PM persona with
  nothing durable to review against, review packets that are not self-contained) all still hold —
  scattered intent is uncitable intent, so they follow from the revised framing too.
- **A `## Standing constraints` section is now evidenced**, not speculative. It is the majority of
  what a real project's charter contains.
- **§5's brownfield path should be reframed as consolidation**, and its "mark every line as
  inferred" rule extended: cite the source document per line, since the sources exist and the
  citation is what makes later correction cheap.
- **Unchanged:** the §7 weight objection still stands. This experiment lowers the uncertainty about
  *shape*; it does not make the machinery lighter.

---

## What this does not settle

- **One project is one project.** The 0.6.0 changelog's caution about `packs/foundry-vtt` applies
  verbatim: `foundryvtt-mcp` is unusually well documented, so "the intent already exists, scattered"
  may be a property of this repo rather than a general finding. `Folio` is the obvious second run,
  and it is the differently-shaped one (an app, not a server).
- **The draft is unratified.** Its accuracy as a charter is unverified until a human corrects it;
  what is verified is the template fit, which does not depend on the content being right.
- **The persona question from §10 is untouched.** Nothing here bears on who should own a periodic
  intent review.
- Whether a consolidation-shaped onboarding would have produced a *better* charter than the
  ROADMAP's existing Objective paragraph, or merely a differently-located one.
