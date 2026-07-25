---
id: work/2026-07-foundry-pack
type: plan
status: approved
created: 2026-07-25
body_hash: "sha256:7989e1a38952f55ec16a8d3e2e99727d8c467fd3e3ae9a0f207d8e624f2b886b"
approved_by: nate
approved_at: 2026-07-25
approval_code: 7989e1a3
---

# A Foundry VTT pack, and what a second pack reveals about the kernel

## Goal

When this is done, `packs/foundry-vtt/` exists, `foundryvtt-mcp` is governed by FolioOS
with its architectural reasoning moved out of narrative documents into decisions and
work-state, and three kernel gaps that only a second pack could expose are either closed
or recorded as deliberate limits.

The pack is the smaller half. FolioOS currently has one pack, for one platform, describing
one buildable artifact. Whether the kernel is *general* or merely *shaped like
`ios-swift`* is not answerable from inside that sample. `foundryvtt-mcp` is a distributed
system — a stdio MCP server, a Dockerized sidecar, and a module running inside a
human-operated browser — verified against a live world. It is the useful second sample
precisely because it is differently shaped.

Grounding survey: `evidence/2026-07-25-foundryvtt-mcp-survey.md`.

## Approving this plan approves:

1. **Extending the capability vocabulary** in `kernel/contract/capability-model.md` with a
   capability for an external environment the agent cannot stand up itself, and a
   consequent narrowing of the rule that every workflow floor requires only
   `file-system` — see step 3, and the open question about it below.
2. **Letting packs contribute personas**, which changes a boundary the document contract
   currently leaves implicit (step 4). The alternative — a project override per project —
   is also on the table and is cheaper.
3. **A resolution of `AGENTS.md` vs. generated `CLAUDE.md`** in a live repository, meaning
   a good hand-written file becomes either a build output or an input (step 6). This is
   the step most likely to feel worse before it feels better.
4. **Writing to `foundryvtt-mcp`** — a second repository, on branch
   `claude/folios-approval-hash-6xmg0v`. No push, PR, or merge; those stay checkpoint
   actions and are not named here, so they re-gate.

## Options considered

**On sequencing, relative to `.folioos/work/2026-07-approval-ergonomics/`:**

- **Option A — approval ergonomics first, then this. Recommended.** That plan renames
  `plan_hash` to `body_hash` and requires a recompile of every governed project's
  instruction artifacts. Adopting FolioOS in `foundryvtt-mcp` first means compiling
  against kernel 0.3.0 and recompiling days later, and it means the first plans written in
  a new repository are approved with the 64-character procedure that prompted all of
  this — teaching the workaround as the norm in exactly the repository where the habit
  will set.
- **Option B — this first.** Defensible if the Foundry work is the more urgent, since the
  approval change is a contract change and contract changes want unhurried approval. The
  cost is one recompile and a migration of two or three young plans.
- **Option C — in parallel.** Rejected. Both touch `kernel/` and `MANIFEST.md`, and the
  git standard puts one branch per unit of work; a shared branch across two plan
  directories is how the work-state directories stop meaning anything.

**On pack scope:**

- **Option D — `foundry-vtt`, system-agnostic, with `dnd5e` specifics in a project
  override. Recommended.** The pack holds Foundry-platform truth (document lifecycle,
  `modifyDocument` protocol, module manifests, version pinning, the coupling-tier
  reasoning); `dnd5e` specifics — AC formula evaluation, exhaustion 2014 vs. 2024, the
  activity model — live in the project layer. This matches how `foundryvtt-mcp` itself
  draws the line: its `ROADMAP.md` states "Foundry-level concepts remain reusable
  internally, but the public MCP tools speak D&D 5e," and its explicit non-goals refuse
  system-neutrality at the *product* level while preserving it at the *internal* level.
- **Option E — `foundry-dnd5e`, one pack covering both.** Simpler now, and honest about
  the only consumer. Rejected because the pack would then encode a table's rules choices
  (this table sets NPC HP to maximum) as platform knowledge, and `ROADMAP.md` names that
  exact conflation as a non-goal.
- **Option F — no pack; project overrides only.** Rejected, but it is closer than it
  looks. With one consumer, a pack is speculative generality. It wins on the kernel
  question rather than the Foundry question: the second pack is the instrument for
  measuring the kernel, and F provides no instrument.

## Steps

Steps 1–5 are kernel and pack work in the FolioOS repository. Steps 6–8 are in
`foundryvtt-mcp`. Steps 3, 4, and 5 are the ones that matter beyond Foundry.

### 1. Create the pack skeleton
- **Files:** `packs/foundry-vtt/pack.md` (new), `packs/foundry-vtt/VERSION` (new, `0.1.0`)
- **What & why:** Mirror `packs/ios-swift/pack.md` exactly — positions taken as ADR-able
  defaults, a maintenance rhythm, a contents line. The Foundry rhythm is version-driven,
  not annual: the pack turns over on Foundry major builds and `dnd5e` minors, which is a
  different cadence from WWDC and is worth stating as such.
- **Risk:** none
- **Model:** implementation · **Effort:** S

### 2. Promote the four durable standards, plus two to the kernel
- **Files:** `packs/foundry-vtt/standards/coupling-tiers.md` (new),
  `.../mutation-pattern.md` (new), `.../version-targeting.md` (new),
  `.../deployment-integrity.md` (new), `.../knowledge/common-pitfalls.md` (new),
  `.../knowledge/api-surfaces.md` (new), `.../checklists/visibility-review.md` (new),
  `kernel/standards/code-quality.md` (edit)
- **What & why:** The survey maps each item to its slot; the content already exists in
  `AGENTS.md`, `PRIMER.md`, `ROADMAP.md`, and `FINDINGS.md` and is being relocated, not
  invented. Two items in the survey are not Foundry-specific and belong in the kernel:
  **the receipt rule** (read the changed record back and report before/after values before
  claiming success — general to any agent mutating external state, and `code-quality.md`
  does not say it) and **the deploy-integrity rule** (build inputs must be derivable from
  the repository alone, never from files left on a host). Promoting the first to
  `code-quality.md` is the smaller, better-evidenced move; the second is arguably kernel
  too but I would leave it in the pack for now and let a third project decide.
  `visibility-review.md` encodes `ROADMAP.md` Phase 5's discipline, which is the highest-
  consequence write on that roadmap for a non-rules reason worth quoting in the checklist:
  a wrong hit point value is corrected in seconds; DM notes rendered visible to a player
  cannot be un-seen.
- **Risk:** ⚠️ Editing `kernel/standards/code-quality.md` is `scope: always`, so it
  consumes the adapter's 150-line `CLAUDE.md` budget. The receipt rule must land as one
  bullet, or something must be demoted — the compile procedure says never widen the budget.
- **Model:** reasoning · **Effort:** L

### 3. Add a capability for an environment the agent cannot provision
- **Files:** `kernel/contract/capability-model.md` (edit),
  `adapters/claude-code/capabilities.md` (edit)
- **What & why:** `run-app` and `screenshots` assume a launchable local artifact.
  Verifying a Foundry write requires a deployed sidecar plus a hard-refreshed active GM
  browser session holding a 45-second bridge token — a human-attended, external
  precondition. No capability describes it, so no workflow that needs it can declare a
  ladder. Proposed: `live-environment` — "a deployed, externally-hosted instance of the
  system under test, whose availability the agent cannot establish and must probe."

  Its consequence is the real content of this step. Capability-model rule 1 says every
  workflow's floor requires only `file-system`. For QA of a live-world write, there is no
  file-system-only floor that verifies anything; the honest floor is *"produce the
  verification script and the expected receipt, and stop for a human to run it."* That is
  a floor that produces an artifact rather than an outcome, and the rule as written does
  not admit it. Amend rule 1 to say so explicitly, rather than letting every Foundry
  workflow quietly violate it.
- **Risk:** ⚠️ Contract change (`overridable: false`). Amending rule 1 loosens a
  deliberate constraint; the wording must keep "a floor that hands work back to the human
  with an artifact" distinct from "a floor that skips verification."
- **Model:** reasoning · **Effort:** M

### 4. Decide whether packs may contribute personas
- **Files:** `kernel/contract/document-contract.md` (edit) or
  `packs/foundry-vtt/personas/foundry-systems-engineer.md` (new), depending on the outcome
- **What & why:** Review quality depends on a reviewer who knows the platform. The
  `ios-swift` pack gets `staff-ios-engineer` and `hig-expert` from `kernel/personas/`,
  which works only because the kernel's persona set was written alongside the only pack.
  Nothing in the document contract forbids a pack persona; nothing enables it either — the
  cascade adds "all non-override pack documents (new `id`s)," so it arguably already works
  and simply isn't stated. Options: (a) state that packs may contribute personas and that
  the review pipeline picks up any persona in the merged set — one sentence, and it makes
  explicit what the cascade already does; (b) keep personas kernel-only and require a
  project override per project, which duplicates the persona for every Foundry project.
  **Recommend (a).** Either way the ambiguity gets resolved in writing, because "arguably
  already works" is how two adapters end up disagreeing.
- **Risk:** none — (a) is a clarification of existing cascade behavior
- **Model:** reasoning · **Effort:** S

### 5. Give the testing policy a row for an unmockable boundary
- **Files:** `kernel/standards/testing-policy.md` (edit)
- **What & why:** The policy's rows cover logic-bearing code, bug fixes, system features,
  and pure UI. `foundryvtt-mcp` has a fifth kind: a transport and bridge layer that cannot
  be unit-tested without inventing a fake protocol, whose own Phase 1 lists "the first
  tests that exercise something other than pure helpers" as outstanding. The gap is not
  laziness, it is a missing bar. Proposed row: *external-protocol boundaries* — extract and
  unit-test the pure decision logic (which `sidecar/confirmation.js`, `actor-utils.js`, and
  `bridge-auth.js` already demonstrate), then require one live smoke check per protocol
  operation with its receipt recorded as evidence. The extractability of the pure core is
  the testable assertion, and this project is the existence proof.
- **Risk:** ⚠️ `scope: always` — same 150-line budget pressure as step 2.
- **Model:** reasoning · **Effort:** M

### 6. Adopt FolioOS in `foundryvtt-mcp`, and resolve `AGENTS.md`
- **Files (in `foundryvtt-mcp`):** `.folioos/**` (new, from
  `userland/project-template/.folioos/`), `.folioos/pin.md` (edit), `.gitignore` (edit,
  append the template's `gitignore-snippet`), `AGENTS.md` (edit)
- **What & why:** Copy the template, pin the manifest release, run the claude-code compile
  procedure. The unresolved question is `AGENTS.md`: 56 lines, hand-written, accurate, and
  the file Codex reads — and the compile procedure wants to emit a generated 150-line
  `CLAUDE.md` covering the same ground. The adapter output table has no `AGENTS.md` row at
  all. Proposed resolution: `AGENTS.md` becomes a *source* — its project-specific rules
  (the coupling policy, the write pattern, the secrets rule) move into
  `.folioos/overrides/` as `type: override` documents with rationales, and `AGENTS.md`
  becomes a compile output alongside `CLAUDE.md`, both stamped per compile-procedure step
  5. That requires an `AGENTS.md` row in `adapters/codex/`, which is a stub today — so
  this step is gated on how much of the codex adapter is willing to exist.
- **Risk:** ⚠️ Regressing a working file. `AGENTS.md` is currently good and is being
  replaced by generated content; if the compile output is worse, the project is worse off
  and the methodology gets blamed. Mitigation: keep the pre-adoption file as
  `evidence/2026-07-25-agents-md-preadoption.md` and diff the compile output against it
  rule by rule. If the generated file loses a rule, the compile procedure is wrong, not
  `AGENTS.md`.
- **Model:** implementation · **Effort:** L

### 7. Convert `FINDINGS.md` reasoning into ADRs; leave the data
- **Files (in `foundryvtt-mcp`):** `.folioos/decisions/000{1..n}-*.md` (new),
  `FINDINGS.md` (edit)
- **What & why:** `FINDINGS.md` does ADR work — decisions with context, alternatives, and
  consequences — without ADR structure, which is why `SPEC.md` had to be marked historical
  rather than superseded decision by decision. Extract the decisions that are still load-
  bearing (coupling tiers; preview/confirm/apply over raw `modifyDocument`; the
  browser-side bridge for prepared data; sidecar-side write gating; Plutonium demoted from
  dependency to recommendation) as dated ADRs. **Inspection data stays in `FINDINGS.md`** —
  it is evidence, not decisions, and the two have different lifecycles under the
  work-state contract. This step is scoped to files, not to re-opening any decision.
- **Risk:** ⚠️ Reconstructing rationale after the fact risks writing a tidier reason than
  the real one. Where the record does not say why, the ADR says "rationale reconstructed
  2026-07-25" rather than inventing one.
- **Model:** reasoning · **Effort:** M

### 8. Open the first governed plan in `foundryvtt-mcp`
- **Files (in `foundryvtt-mcp`):** `.folioos/work/2026-07-sidecar-write-gate/plan.md` (new,
  `status: draft`)
- **What & why:** Adoption is unproven until one real plan runs through it. The natural
  first unit is `ROADMAP.md` Phase 1's first bullet — enforce write gating in the sidecar,
  not only in `src/tools/write.ts`, because an API-key holder can currently call
  `POST /api/mcp/actors/:id/delete` directly. It is small, security-bearing, has an
  obvious test, and is Phase 1's gate for Phases 2, 5, and 6.

  **This step produces a draft plan and stops.** It does not fix the gap. Writing the plan
  is the whole step.
- **Risk:** none — no behavior change
- **Model:** reasoning · **Effort:** M

### 9. Version, manifest, changelog
- **Files:** `kernel/VERSION` (edit), `MANIFEST.md` (edit), `CHANGELOG.md` (edit),
  `.folioos/decisions/0008-second-pack-kernel-boundaries.md` (new)
- **What & why:** Kernel minor for steps 2–5; `packs/foundry-vtt` enters the manifest at
  0.1.0; a new manifest release row pins the tested combination. ADR-0008 records what the
  second pack taught — the capability gap, the persona boundary, the testing-policy gap —
  so the next pack starts from a kernel that has been measured twice.
- **Risk:** none
- **Model:** utility · **Effort:** S

## Test plan

- **Contract validation checklist** (`kernel/contract/document-contract.md`) over the full
  merged set: kernel ⊕ `foundry-vtt` ⊕ `foundryvtt-mcp` overrides. Every `id` matches its
  path; every override names an existing `overridable: true` target with a non-empty
  rationale; no two same-layer overrides share a target.
- **Cascade dry-run, by hand.** Resolve the merged set for `foundryvtt-mcp` and log every
  override applied with target, operation, and rationale, per compile-procedure step 2.
  Record as evidence. A pack that cannot be resolved by hand fails the files-first
  requirement of ADR-0006 regardless of what it contains.
- **`CLAUDE.md` budget.** Compile and count. Over 150 lines is a build failure; steps 2 and
  5 both add `always`-scoped content, so this is the step most likely to fail, and the
  resolution is demotion, never a wider budget.
- **`AGENTS.md` rule-by-rule diff** against the preserved pre-adoption copy. All four
  Critical Rules, the coupling policy, the write pattern, and the secrets rule must survive
  into the compiled artifacts. A dropped rule fails the step.
- **No behavior change in `foundryvtt-mcp`.** `npm test` (build + `node --test` over
  sidecar and module suites) passes unchanged, and the diff touches no file under `src/`,
  `sidecar/`, or `module/`. This plan is documentation and governance only; a source diff
  means scope escaped.
- **Secrets scan still passes.** The project's tests assert shared credential strings are
  absent from source. The new `.folioos/` files and every ADR reconstructed from
  `FINDINGS.md` are in scope for that assertion — reconstructing deployment rationale is a
  plausible way to paste a host name or key into a document.
- **Persona smoke, if step 4 goes to (a).** Run `kernel/workflows/review-pipeline.md`
  against one small real diff with the pack persona in the merged set, and confirm the
  pipeline picks it up without an adapter change. If it needs one, (a) was the wrong answer.

No unit tests: no code changes. Per `kernel/standards/testing-policy.md`, mechanical and
generated-doc changes need nothing beyond the build passing — which here is the compile
procedure and the validation checklist.

## Risks & open questions

- **Amending capability-model rule 1 is the riskiest thing in this plan, and it is
  load-bearing for the least interesting reason.** The rule exists to stop workflows from
  being adapter features in disguise, and I am proposing to loosen it because one project's
  QA genuinely cannot reach a file-system-only floor. That is a real gap, but "the rule is
  inconvenient for my project" and "the rule is wrong" look identical from inside the
  project. If you would rather hold the line, the alternative is that Foundry QA is not a
  kernel workflow at all and lives in the pack as a checklist — which costs the ladder and
  keeps the contract clean. I lean toward amending; I am not confident.
- **One consumer is thin evidence for a pack.** Everything in `packs/foundry-vtt/` would
  come from a single repository, and pack-versus-project is the boundary a single sample
  cannot settle. The kernel findings are what justify the work; if steps 3–5 get rejected,
  reconsider Option F and keep this as project overrides.
- **Step 6 can make a good file worse.** Stated in the step with a mitigation, repeated
  here because it is the most likely way this plan produces a visible regression.
- **Open question: does the pack cover Foundry v14 only, or v13 too?** `foundryvtt-mcp`
  pins v14 build 365, and the `extraHeaders`/`query` pitfall is version-specific. A pack
  pinned to one major is honest and narrow; one that spans majors needs per-version
  annotations the `ios-swift` pack deliberately avoids ("knowledge entries that an OS
  release invalidates are deleted, not annotated"). I would pin to v14 and delete on
  upgrade, matching the existing pack's discipline.
- **Open question: should the receipt rule be promoted in this plan at all?** It is a
  kernel `always`-scoped edit riding along with pack work, and it competes for the
  `CLAUDE.md` budget. Cleanly, it is its own candidate and its own promotion cycle. I
  folded it into step 2 because the evidence is right here; say so if you would rather it
  went through the normal loop.
- **`FINDINGS.md` may contain deployment details that should not be reproduced.** The
  secrets test covers source files. Step 7 reads a document full of live-deployment
  inspection data and writes new documents; the test plan extends the scan, but a human
  reading the ADR diff is the real check.

## Out of scope

- **Any change to `src/`, `sidecar/`, or `module/`.** No Phase 1 fix, no write-gate
  implementation. Step 8 produces a plan for the write gate and stops; executing it is a
  separate approval.
- **Pushing, opening PRs, or merging in either repository.** Outward-facing writes, not
  named here, so they re-gate — including for the FolioOS branch this plan sits on.
- **The `dnd5e` system pack.** Deliberately excluded per Option D; system specifics go to
  the project layer until a second Foundry project exists to justify a pack.
- **Completing the codex or gemini adapters.** Step 6 needs one `AGENTS.md` row from the
  codex adapter, not the adapter.
- **`ROADMAP.md` Phases 2–8.** The roadmap is the project's own planning artifact and this
  plan does not restructure it. If adoption goes well, phases become work directories as
  they are picked up — one at a time, not converted wholesale.

## Re-gate log
