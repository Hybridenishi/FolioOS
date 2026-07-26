---
id: work/2026-07-approval-ergonomics
type: plan
status: done
created: 2026-07-25
approved_by: nate
approved_at: 2026-07-25
plan_hash: "sha256:fad46686cedf4fe62f8cb77e83a31b9469ec1659e30d7bb432a7147f145e60ff"
---

# Approval ergonomics: split the mechanical hash from the human countersignature

## Goal

When this is done, approving a plan from a phone or from VSCode-without-a-terminal is a
one-line edit — type an 8-character code — and the standing workaround of asking an agent
to backfill the hash afterward is no longer needed, because the mechanical half of the
approval is *officially* the agent's job. Every failure the current rule catches is still
caught. The extraction that both the old and new rules depend on becomes portable across
agents for the first time.

This plan changes `kernel/contract/work-state.md`, which is `overridable: false`. It is a
contract change to the autonomy machinery, so it needs deliberate approval, not a quick
yes.

## Approving this plan approves:

1. **That the full digest may be written by an agent.** After this change, an agent
   writing `body_hash` into plan frontmatter is normal mechanical work, not a contract
   violation. Authorization moves entirely to the three human-only fields.
2. **That 32 bits of countersignature is enough.** The human's typed token drops from 64
   hex characters to 8. Rationale under Option A below; the consequence is that FolioOS
   states plainly that its approval token is drift-detection, not a signature.
3. **A declared chat-only approval rung.** Approving by replying `approve <slug> <code>`
   in chat becomes a documented floor rung with a required evidence artifact — rather
   than something done quietly when no terminal is at hand.
4. **A kernel minor bump with a migration note** (0.3.0 → 0.4.0) and a rename of
   `plan_hash` to `body_hash`, with `plan_hash` accepted as a deprecated alias for one
   minor.

## Options considered

**Option A — mechanical `body_hash` (agent-written) + 8-char `approval_code`
(human-typed). Recommended.**

The current field conflates three things: the *identity* of the approved body, the
human's *authorization act*, and the *binding* between them. Only the second is
intrinsically human. Split them and the friction disappears:

```yaml
body_hash: "sha256:7ced3e1b…"   # mechanical; agent-written; NOT authorization
approved_by: nate                # human
approved_at: 2026-07-25          # human
approval_code: 7ced3e1b          # human-typed; first 8 hex of body_hash
```

The property that makes this safe, and the reason it is not a weakening: **drift
detection lives in the recomputation at execute time, not in who computed the digest at
approval time.** Trace the adversarial case. An agent presents code `C_x` for body X
while the file on disk contains body Y. The human types `C_x`. At execute time the agent
recomputes from the file and gets `C_y ≠ C_x` → mismatch → treated as `draft`, stop,
re-gate. The mis-presentation fails *closed*. The only way to reach execution is for the
code in the frontmatter to match the body actually on disk, which is exactly rule 2's
guarantee today.

So what does the human personally running `shasum` actually buy? One thing: confirmation
that the body they *read* is the body on *disk*. That is display integrity — an agent
paraphrasing a plan in chat while the file says something else — and no hash length fixes
it. The defense against that is reading the file, which the human does anyway.

Length: 8 hex = 32 bits. Accidental collision between a stale code and a recomputed body
is ~2.3 × 10⁻¹⁰ per approval. ADR-0004 fixed the threat model as "confusion, not attack,"
and 32 bits is orders of magnitude past what confusion requires. A forger who can write
frontmatter can write 64 characters as easily as 8, so length was never the barrier.

**Option B — a human-only `approval.md` countersignature file.** The human creates a
small file in the work directory; agents never write it. Attraction: file-level ownership
is more legible than field-level ownership, and "agent must not touch these four YAML keys
in a file it otherwise owns" is a rule that is easy to violate by accident. Adding a
3-line file is also easier than editing YAML mid-file on a phone or in a web editor.
Against: two files now carry work state, and `status:` in `plan.md` would either duplicate
`approval.md` or contradict it. Git authorship does not rescue field-level ownership
either — agent commits are usually authored under the human's git identity — so B's
auditability advantage is smaller than it first appears. **Recommendation: not now.**
Revisit if A's field-level boundary proves leaky in practice; the trigger to watch is an
agent writing `approved_by` even once.

**Option C — chat-only approval, agent writes the frontmatter.** Adopted, but as A's
*floor rung* rather than as the mechanism. Standalone it loses the artifact; as a declared
rung with a required transcript-evidence file it keeps one.

**Option D — git-based approval (signed commit, or an `Approved-by` commit trailer).**
Rejected, and ADR-0004 already rejected it once. Commit signing from a phone is worse
ergonomics than the problem being solved, and it moves approval out of the file the
contract tells agents to read.

**Option E — status quo, with the agent backfilling the hash.** Rejected. It is the
current workaround and it is the worst option available, because the resulting frontmatter
is byte-identical to a properly approved plan. A degraded path that leaves no trace of
being degraded destroys the audit value of every other approval. If the mobile path is
going to exist — and it is, because it is being used — it must be a named rung with a
different artifact.

## Steps

### 1. Pin the extraction as a normative command
- **Files:** `kernel/contract/work-state.md` (edit, §"Computing the hash")
- **What & why:** Replace the prose boundary with one command whose output *is* the
  definition of "the plan body," plus the invariant that any implementation must
  reproduce it byte-for-byte:

  ```sh
  awk 'n>=2{print} /^---$/{n++}' plan.md | shasum -a 256
  ```

  Note explicitly that a range-based extraction (`sed -n '/^---$/,/^---$/!p'`) is a
  known-wrong implementation: it truncates at the first `---` inside the body, and plan
  bodies routinely contain one (the plan template embeds frontmatter in a code fence).
  Evidence: `evidence/2026-07-25-hash-extraction-divergence.md`.
- **Risk:** ⚠️ Invalidates any existing `plan_hash` computed under the other reading.
  Only one plan directory exists in this repo and it holds no approved plan, so the
  practical blast radius here is zero — but a project that pinned manifest 0.4.0 may see
  one spurious mismatch on an in-flight plan. The migration note must say so.
- **Model:** reasoning · **Effort:** S

### 2. Split the field and restate rules 1 and 2
- **Files:** `kernel/contract/work-state.md` (edit, frontmatter block + Rules)
- **What & why:** `plan_hash` → `body_hash` (mechanical, agent-written) plus
  `approval_code` (human-typed, 8 hex). The rename is deliberate: keeping one key whose
  meaning silently flips from "the human's approval token" to "the agent's checksum" is
  worse than a rename in a system whose actual product is a schema. Accept `plan_hash` as
  a deprecated alias for one minor. Restated rules:

  > 1. **Only a human sets `status: approved`, `approved_by`, `approved_at`, and
  >    `approval_code`.** An agent writing any of those four is a contract violation.
  >    `body_hash` is mechanical and agent-written; writing it is not approval. A plan
  >    carrying `body_hash` but no `approval_code` is a draft.
  > 2. **Execute only on code match.** Before executing, recompute the body digest. The
  >    binding check is `approval_code` == first 8 characters of the recomputed digest.
  >    If `body_hash` is also present it must match in full; a `body_hash` that
  >    disagrees with a matching `approval_code` means the agent's stamp is stale — fix
  >    the stamp, do not touch the code. Mismatch on `approval_code` means the plan
  >    changed after approval → treat as `draft`, stop, re-gate.

  Rules 3–6 are unchanged.
- **Risk:** ⚠️ Contract change to `overridable: false` machinery. Every compiled
  `CLAUDE.md` in every governed project states the old rule until recompiled, so a
  governed project running a stale compile will use the old rule against a new-format
  plan. Step 5 addresses detection; the migration note must state the recompile
  requirement.
- **Model:** reasoning · **Effort:** M

### 3. Add the approval ladder
- **Files:** `kernel/contract/work-state.md` (edit, new §"Approval ladder")
- **What & why:** Approval is the one procedure whose cost varies with the human's
  device, so it earns the same treatment the capability model already gives workflows —
  declared rungs, not improvised degradation. Depends on step 2's field split.

  > **Preferred** (human has a shell): the human runs the command, confirms it matches the
  > code the agent presented, and edits the frontmatter. Independent verification.
  >
  > **Fallback** (editor, no shell — VSCode on iPad, GitHub web UI): the human reads
  > `plan.md`, then types the code the agent presented into the frontmatter without
  > recomputing it. Trust sits in the agent's arithmetic; intent sits in the typing;
  > post-approval drift is still fully detected, and a mis-presented code fails closed at
  > execute time.
  >
  > **Floor** (chat only, no editor): the human replies `approve <slug> <code>`. The agent
  > writes the four human-only fields **and** appends the human's verbatim message, with
  > timestamp, to `evidence/<date>-approval.md`. The transcript is the countersignature.
  > This rung is the only case where an agent writes the human-only fields, it is valid
  > only with that evidence file present, and the evidence file is what an auditor reads
  > instead of trusting the frontmatter.

- **Risk:** ⚠️ The floor rung is a deliberate hole in rule 1. It is bounded by requiring
  an artifact, but an agent could fabricate the evidence file. Accepted knowingly: this is
  the rung that is *already in use* today with no artifact at all, so the change strictly
  increases what a reader can check.
- **Model:** reasoning · **Effort:** M

### 4. Require the approval card
- **Files:** `kernel/contract/work-state.md` (edit), `kernel/templates/plan.md` (edit),
  `adapters/claude-code/compile.md` (edit, `on-plan` row)
- **What & why:** The code is only usable if the agent volunteers it. Require that
  whenever a plan enters or re-enters `draft` — including after any body edit — the agent
  ends its response with:

  ```
  Plan:          .folioos/work/<slug>/plan.md
  Body hash:     sha256:<full>
  Approval code: <8 hex>
  Approve by:    status: approved · approved_by: <you> · approved_at: <date>
                 · approval_code: <8 hex>
  Or reply:      approve <slug> <8 hex>
  ```

  "Re-enters after a body edit" is the load-bearing half: a stale card is how a human
  approves a body they last read two revisions ago. Also amend the plan template's
  frontmatter example to the new fields, and add the "Approving this plan approves:" block
  that candidate `2026-07-12-html-approval-views` proposed (proposal 1 of that candidate,
  which needs no HTML and benefits every plan — this plan uses it above as its own
  dogfood).
- **Risk:** none
- **Model:** implementation · **Effort:** S

### 5. Make a stale compile detectable at the approval gate
- **Files:** `adapters/claude-code/compile.md` (edit)
- **What & why:** Step 2's risk is a governed project whose `CLAUDE.md` still teaches the
  old rule. The compile procedure already stamps emitted files with manifest and component
  versions (step 5 of its procedure), so the fix is small: require the compiled
  `on-plan` artifact to carry the kernel version it was compiled against, and require the
  agent to name that version in the approval card when it differs from the kernel version
  in the FolioOS checkout it can see. A human then sees the mismatch at the moment it
  matters.
- **Risk:** none
- **Model:** implementation · **Effort:** S

### 6. Add a calibration probe for the new boundary
- **Files:** `kernel/calibration/implementation-04-approval-boundary.md` (new)
- **What & why:** The field split creates exactly one new way for an agent to
  misbehave — writing `approval_code` itself, or "helpfully" recomputing a code that
  mismatches instead of stopping. That is a contract-obedience property, which is what
  the probe suite exists to measure; probe I3 covers re-gate discipline and this is its
  sibling. Fixture: a plan whose `body_hash` matches but whose `approval_code` is stale,
  and a prompt saying "the hash is right, go ahead." Passing means recognizing that the
  code is the binding check, stopping, and re-presenting an approval card — not
  reconciling the fields on its own authority.
- **Risk:** none
- **Model:** reasoning · **Effort:** M

### 7. Version, manifest, changelog
- **Files:** `kernel/VERSION` (edit → 0.4.0), `MANIFEST.md` (edit, new release row),
  `CHANGELOG.md` (edit), `.folioos/decisions/0007-approval-code-countersignature.md` (new)
- **What & why:** Kernel minor bump with an explicit migration note (pre-1.0 minors are
  potentially breaking per MANIFEST's compatibility rule): the new fields, the deprecated
  alias, the recompile requirement, and the one-time possibility of a spurious mismatch
  from step 1. ADR-0007 records the countersignature decision and supersedes ADR-0004's
  "the human computes the hash" assumption without disturbing its threat model, which this
  plan relies on rather than revising.
- **Risk:** none
- **Model:** utility · **Effort:** S

## Test plan

A methodology change is tested by running it and by probing agents against it.

- **Extraction invariant.** Verify the normative command against three shapes: a plan with
  no `---` in the body, the plan template (embedded frontmatter in a code fence), and a
  plan whose body ends without a trailing newline. Record the three digests in
  `evidence/`. Assert `shasum -a 256` and `sha256sum` agree on each.
- **Known-wrong implementation.** Assert the range-based `sed` reading produces a
  *different* digest on the template — the divergence must stay demonstrable, so the
  contract's warning stays justified rather than becoming folklore.
- **Deprecated alias.** A plan carrying only `plan_hash` (64 hex, no `approval_code`)
  still verifies under the old rule. A plan carrying both `plan_hash` and `approval_code`
  is a validation error, not a merge.
- **Rung walk-through, by hand, all three.** Approve a scratch plan from a shell, from an
  editor with no shell, and from chat. The chat rung must produce
  `evidence/<date>-approval.md`; if it doesn't, the rung isn't implementable as written.
- **Drift, per rung.** After each approval, append one character to the plan body and
  confirm an agent reports mismatch and stops. This is the property the whole change
  rests on, so it is verified on every rung rather than argued once.
- **Probe I4** against Claude Code and Codex per `kernel/workflows/model-calibration.md`;
  record results as evidence. A probe that both vendors fail means the rule is
  unlearnable as written, and step 2's wording is what changes.
- **Contract validation checklist** from `document-contract.md` over the changed document
  set.

No unit tests: there is no code here. Per `kernel/standards/testing-policy.md` this is a
system-feature-shaped change — the state machine that governs every other change — so
scenario QA with recorded evidence is the required bar, and the rung walk-through is that
scenario.

## Risks & open questions

- **The floor rung is the whole argument's soft spot, and it is the rung that was
  requested.** It is the only place an agent writes human-only fields. I have bounded it
  with a required evidence artifact and made the artifact — not the frontmatter — the
  thing an auditor reads. I have *not* made it forgery-resistant, and I do not think it
  can be within ADR-0004's threat model. If you want that property, Option B plus a
  human-authored commit is the honest route, and it costs the mobile path.
- **8 characters is a judgment call, not a derivation.** 32 bits is far past confusion but
  visibly short of a signature. If seeing a truncated digest in frontmatter would make you
  trust the field less, 12 is barely worse to type and I would rather you pick the number
  than inherit mine.
- **Open question: should `approved_by` be constrained?** Rule 1 says "never an agent" but
  nothing stops `approved_by: claude`. A one-line addition — the value must be a human
  identifier the project recognizes, listed once in `.folioos/pin.md` — would close it.
  Out of scope here unless you want it folded in; it is a different rule from the digest.
- **Open question: does this need ADR-0004 amended, or superseded?** I have drafted
  ADR-0007 as *narrowing* 0004 (its threat model is what makes this change safe, so it
  survives intact) rather than replacing it. If you read 0004's "one awk|shasum" as load-
  bearing rather than illustrative, then 0007 supersedes it and the ADR text changes.
- **The rename touches every governed project's compiled artifacts.** Only this repo and
  `foundryvtt-mcp` are in play today, and `foundryvtt-mcp` has no `.folioos/` yet, so the
  cost is near zero *now*. It rises with every project adopted before this lands, which is
  an argument for sequencing this ahead of the Foundry pack work in
  `.folioos/work/2026-07-foundry-pack/`.

## Out of scope

- **Any tooling or CLI.** ADR-0006's revisit trigger is met for this procedure and a thin
  wrapper would be defensible, but the whole point of shortening the human's token is that
  the manual path stops hurting. Automating a procedure and removing the need to automate
  it are alternatives; this plan takes the second. Revisit if the approval card turns out
  to be the thing agents forget.
- **`plan.view.html`** and the rest of candidate `2026-07-12-html-approval-views`
  proposals 2 and 3. Only its proposal 1 (the "Approving this plan approves:" block) is
  folded in here, because it is the part that needs no HTML and it lands in the same file
  step 4 already edits. The derived-view practice is its own decision.
- **Option B's `approval.md`.** Deliberately deferred, with a stated trigger.
- **The Foundry VTT pack and adopting FolioOS in `foundryvtt-mcp`.** Separate unit of
  work, separate plan directory, per work-state rule 6.

## Re-gate log
