---
id: candidates/2026-07-25-approval-code-ergonomics
type: knowledge
layer: project
scope: on-demand
status: candidate
proposed_target: kernel/contract/work-state
---
**Learning:** A hash's drift-detection property comes entirely from *recomputation at
execute time*, not from who computed it at approval time. So the human's half of an
approval does not need to be a 64-character transcription — it only needs to be a token
they could not have produced without seeing the plan. The full hash can be
agent-written (mechanical, not authorization) while the human types a short truncation
as countersignature; every failure the current rule catches is still caught, and it is
caught fail-closed. Requiring the human to be the one who runs `shasum` buys exactly one
extra property — confirming that the body they *read* is the body on *disk* — and that
is display integrity, which no hash length fixes.

**Evidence:** Reported friction from the FolioOS author (2026-07-25): computing the hash
from VSCode or mobile is impractical, and the standing workaround is to ask the agent to
supply the hash afterward — which silently voids the "the human saw this body" property
while leaving the frontmatter looking correct. A degraded procedure that is
indistinguishable from the real one is worse than an explicitly degraded rung.
Corroborating design precedent in a live governed project: `foundryvtt-mcp` gates every
mutation behind a scoped, single-use, short-lived confirmation token
(`sidecar/confirmation.js`) that the *machine* issues and the *caller* echoes back — the
same decomposition, already dogfooded, with the human never transcribing a digest.
ADR-0006 pre-authorizes this revisit: "the same manual procedure performed often enough
to be error-prone in practice — automate that procedure first."

**Proposed encoding:** Amend `kernel/contract/work-state.md` (contract change, kernel
minor bump pre-1.0) to split the current `plan_hash` field into a mechanical
agent-written `body_hash` and a human-written 8-character `approval_code`; restate rule 1
in terms of which *fields* are human-only rather than which *file*; add a three-rung
approval ladder (shell / editor-only / chat-only) in the capability-ladder idiom so the
mobile path is a declared rung with declared evidence rather than an undocumented
shortcut; and require agents to emit an approval card naming the code whenever a plan
enters or re-enters `draft`. Full drafted text in
`.folioos/work/2026-07-approval-ergonomics/plan.md`.
