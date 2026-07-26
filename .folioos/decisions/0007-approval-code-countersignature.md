# ADR-0007: The human's half of an approval is an 8-character countersignature

- **Date:** 2026-07-25
- **Status:** accepted
- **Narrows:** [ADR-0004](0004-approval-as-artifact.md) (which remains accepted; its threat model is what makes this safe)

## Context

ADR-0004 made approval an artifact: a human sets `status: approved` and records a sha256 of the plan body. In practice the 64-character transcription is not performable from the devices approvals actually happen on — a tablet editor, a phone, a web UI with no shell. The standing workaround was to ask an agent to supply the digest afterward, which produces frontmatter **byte-identical to a properly approved plan**. A degraded path indistinguishable from the real one is worse than no path: it silently voids the "the human saw this body" property across the whole log, and it cannot be audited because it leaves no trace.

ADR-0006 pre-authorized this revisit: "the same manual procedure performed often enough to be error-prone in practice — automate that procedure first."

## Decision

Split `plan_hash` into `body_hash` (full digest, agent-written, mechanical, explicitly *not* authorization) and `approval_code` (first 8 hex, human-typed, the binding check). Add a three-rung approval ladder — shell / editor-only / chat-only — where the floor rung permits an agent to transcribe a chat approval **only** with an `evidence/<yyyy-mm-dd>-approval.md` artifact recording the human's verbatim message. Pin the body-extraction command normatively. Kernel 0.4.0.

The load-bearing argument: **drift detection comes entirely from recomputing the digest at execute time, not from who computed it at approval time.** An agent that presents a code for a body other than the one on disk fails *closed* — the recompute mismatches and execution stops. So the human personally running `shasum` buys exactly one thing beyond the 8-character version: confirmation that the body they *read* is the body on *disk*. That is display integrity, and no digest length addresses it. What the human's token must be is unforgeable-by-accident evidence they saw the plan; 32 bits is orders of magnitude past what ADR-0004's threat model (confusion, not attack) requires. A forger who can write frontmatter writes 64 characters as easily as 8, so length was never the barrier.

## Alternatives considered

- **A human-only `approval.md` countersignature file.** File-level ownership is more legible than "don't touch these four YAML keys," and creating a small file beats editing mid-file YAML on a phone. Rejected for now: two files carrying work state invites contradiction, and git authorship does not actually rescue auditability since agent commits usually carry the human's identity. Trigger to revisit: an agent writing `approved_by` even once outside the floor rung.
- **Chat-only approval as the mechanism** rather than as a declared floor rung. Rejected: loses the artifact entirely.
- **Signed commits or an `Approved-by:` trailer.** Rejected again, as in ADR-0004. Commit signing from a phone is worse ergonomics than the problem being solved, and it moves approval out of the file agents are told to read.
- **Status quo plus agent backfill.** Rejected — it is the workaround, and its defect is that it is invisible.

## Consequences

Approval from any device is a one-line edit or one chat message. The audit story changes shape: the frontmatter alone no longer distinguishes a shell-verified approval from a chat-transcribed one, so the evidence file becomes the thing an auditor reads on the floor rung. That rung is a knowing, bounded hole in rule 1 and is not forgery-resistant.

Costs: a field rename that requires every governed project to recompile its instruction artifacts; a deprecated-alias window of one minor; a possible one-time spurious mismatch on in-flight plans, since pinning the extraction may disagree with whichever reading their hash was computed under. New probe I4 covers the failure mode the split introduces — an agent reconciling `body_hash` and `approval_code` instead of stopping.

This ADR was itself approved on the floor rung, before that rung existed, and the irregularity is recorded in `.folioos/work/2026-07-approval-ergonomics/evidence/2026-07-25-approval.md`. The bootstrap is intrinsic: a change to the approval procedure can only be approved under the procedure it replaces.
