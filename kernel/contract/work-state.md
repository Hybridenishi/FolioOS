---
id: kernel/contract/work-state
type: contract
layer: kernel
scope: always
requires: []
overridable: false
version: 2
---

# Work-State Contract

The plan→approve→execute contract only works across agents, sessions, and vendors if approval is an **artifact, not a conversational event**. This contract defines that artifact and its lifecycle. Any agent, in any session, answers "am I authorized to execute this?" by reading a file — never by trusting chat history.

## Directory layout (per project)

```
.folioos/
├── work/                          # committed, machine-readable work-state
│   └── 2026-07-import-feature/    # <yyyy-mm>-<slug>, one dir per unit of work
│       ├── plan.md                # THE state machine (frontmatter below)
│       └── evidence/              # append-only: review reports, QA runs, screenshots
├── decisions/                     # ADRs — durable, append-only, dated
├── candidates/                    # learnings awaiting promotion (see knowledge-promotion)
├── overrides/                     # project-layer override documents
└── .scratch/                      # gitignored orchestration debris
```

Three lifecycles, never mixed: **work-state** (mutable frontmatter, committed), **evidence** (append-only, committed), **scratch** (gitignored).

## Plan state machine

`plan.md` frontmatter:

```yaml
---
id: work/2026-07-import-feature
type: plan
status: approved          # draft | approved | executing | blocked | done | abandoned
created: 2026-07-01
body_hash: "sha256:3f9a…" # mechanical: digest of the body. Agent-written. NOT authorization.
approved_by: nate         # human identifier; never an agent
approved_at: 2026-07-02
approval_code: 3f9a1c7e   # human-typed countersignature: first 8 hex of body_hash
---
```

```
draft ──(human approves; code recorded)──▶ approved ──▶ executing ──▶ done
  ▲                                            │             │
  └────────(plan revised; code cleared)────────┴── blocked ◀─┘
                                                (re-gate)      or ▶ abandoned
```

**Why the field is split.** The old single `plan_hash` conflated three things: the *identity* of the approved body, the human's *authorization act*, and the *binding* between them. Only the second is intrinsically human. Drift detection comes entirely from recomputing the digest at execute time, not from who computed it at approval time — an agent that presents a code for a body other than the one on disk fails **closed**, because the recompute mismatches and stops. So the human's half needs only to be a token they could not have produced without seeing the plan. Eight characters is that token. See [ADR-0007](../../.folioos/decisions/0007-approval-code-countersignature.md).

## Rules (non-negotiable; `overridable: false`)

1. **Only a human sets `status: approved`, `approved_by`, `approved_at`, and `approval_code`.** An agent writing any of those four is a contract violation — except on the floor rung of the approval ladder below, which requires its own evidence artifact. `body_hash` is mechanical and agent-written; writing it is not approval. **A plan carrying `body_hash` but no `approval_code` is a draft.**
2. **Execute only on code match.** Before executing, the agent recomputes the body digest by the procedure below. The binding check is `approval_code` == first 8 characters of the recomputed digest. Mismatch means the plan changed after approval → treat as `draft`, stop, re-gate. If `body_hash` is also present it must match in full; a `body_hash` that disagrees with a *matching* `approval_code` means the agent's stamp is stale — restamp `body_hash`, never touch `approval_code`.
3. **The re-gate rule.** If execution surfaces a risk not in the approved plan (touching persistence, deleting beyond planned scope, a new dependency, an outward-facing write), the agent sets `status: blocked`, records why in the plan body under `## Re-gate log`, and stops. Resuming requires a fresh human approval of the revised plan.
4. **Evidence is append-only.** Review reports, QA runs, and screenshots go to `evidence/` with dated filenames. Never edited, never deleted; superseded evidence is superseded by newer files.
5. **Approved means the approved plan.** Execution follows the plan without re-litigating settled choices. Discovering a *better idea* mid-execution is a candidate for `candidates/`, not a license to deviate.
6. One unit of work, one directory. If scope splits, close the plan (`done` or `abandoned`) and open new ones.
7. **A body edit voids approval.** Any change to the plan body after approval changes the digest, which is rule 2 doing its job. The agent reverts the plan to `draft`, clears the four human-only fields, and issues a fresh approval card. It does not "re-approve" on the human's behalf from a prior approval.

## Approval ladder

Approval is the one procedure whose cost depends on the human's device, so it declares rungs rather than leaving degradation to be improvised — same discipline the [capability model](capability-model.md) applies to workflows. The rung used is recorded in the approval evidence when it is not the preferred one.

- **Preferred** (human has a shell). The human runs the command below, confirms the result matches the code the agent presented, and edits the frontmatter. Independent verification: the human's own machine computed the digest.
- **Fallback** (editor, no shell — VSCode on a tablet, GitHub web UI). The human reads `plan.md`, then types the code the agent presented into the frontmatter without recomputing it. Trust sits in the agent's arithmetic; intent sits in the typing. Post-approval drift is still fully detected, and a mis-presented code fails closed at execute time.
- **Floor** (chat only, no editor). The human replies `approve <slug> <code>`. The agent writes the four human-only fields **and** appends the human's verbatim message, with UTC timestamp and the verification it performed, to `evidence/<yyyy-mm-dd>-approval.md`. The transcript is the countersignature. This is the sole exception to rule 1; it is valid **only** with that evidence file present, and an auditor reads the evidence file rather than trusting the frontmatter.

The floor rung is a deliberate, bounded hole in rule 1. It is not forgery-resistant and cannot be within this contract's threat model ([ADR-0004](../../.folioos/decisions/0004-approval-as-artifact.md): confusion, not attack). It exists because the alternative in practice is an undocumented shortcut that leaves no trace at all.

## The approval card

Whenever a plan enters **or re-enters** `draft` — including after any body edit, per rule 7 — the agent ends its response with:

```
Plan:          .folioos/work/<slug>/plan.md
Body hash:     sha256:<full>
Approval code: <8 hex>
Approve by:    status: approved · approved_by: <you> · approved_at: <yyyy-mm-dd>
               · approval_code: <8 hex>
Or reply:      approve <slug> <8 hex>
```

"Re-enters after a body edit" is the load-bearing half: a stale card is how a human approves a body they last read two revisions ago. If the agent's compiled instruction artifact was built against a different kernel version than the FolioOS checkout it can see, the card names both versions — a stale compile is otherwise invisible at the exact moment it matters.

## Computing the hash

The plan body is **defined as** the output of this command. Prose cannot define a byte boundary; this can.

```sh
awk 'n>=2{print} /^---$/{n++}' plan.md | shasum -a 256
```

`sha256sum` may substitute for `shasum -a 256`. Any other implementation must reproduce this command's output byte-for-byte, including the blank line that follows the closing delimiter. Recorded as `sha256:<hex>`; `approval_code` is its first 8 characters, lowercase.

**A range-based extraction is a known-wrong implementation.** `sed -n '/^---$/,/^---$/!p'` truncates the body at the first `---` *inside* it, and plan bodies routinely contain one — the [plan template](../templates/plan.md) embeds frontmatter in a code fence. The `awk` form above counts delimiters instead of pairing them, so embedded `---` lines are printed like any other content. Two faithful-looking readings of the old prose rule hashed the same file to digests sharing no prefix; see `.folioos/work/2026-07-approval-ergonomics/evidence/2026-07-25-hash-extraction-divergence.md`.

## Migration from version 1

Kernel 0.3.0 used a single `plan_hash` holding the full 64-character digest, transcribed by the human.

- **`plan_hash` is accepted as a deprecated alias for one minor.** A plan carrying `plan_hash` and no `approval_code` verifies under the version-1 rule: full 64-character match, no truncation.
- A plan carrying **both** `plan_hash` and `approval_code` is a validation error, not a merge. Pick one schema per plan.
- **Recompile required.** Every governed project's compiled instruction artifacts state the version-1 rule until rebuilt. Until then that project's agent applies the old rule to new-format plans and will find no `plan_hash` — fail-closed, but confusing. Recompile at adoption of manifest 0.5.0.
- **One-time spurious mismatch is possible.** Pinning the extraction command may disagree with whichever reading an in-flight plan's hash was computed under. A mismatch on a plan approved before 2026-07-25 is more likely this, than drift — re-approve rather than investigate.
