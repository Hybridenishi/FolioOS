---
id: candidates/2026-07-25-hash-extraction-underspecified
type: knowledge
layer: project
scope: on-demand
status: candidate
proposed_target: kernel/contract/work-state
---
**Learning:** When a contract's promise is cross-agent portability, any byte-level
boundary it defines has to be pinned as an executable command, not described in prose.
"Content below the frontmatter's closing `---` (exclusive), byte-exact" reads as precise
and is not: it leaves the blank line after the delimiter unassigned, and it does not
distinguish a counting extraction from a range extraction — which matters because plan
bodies routinely contain `---` lines (the plan template embeds frontmatter inside a code
fence). Two agents reading the same sentence faithfully compute different digests of the
same file.

**Evidence:** `.folioos/work/2026-07-approval-ergonomics/evidence/2026-07-25-hash-extraction-divergence.md`
— reproduced against `kernel/templates/plan.md` at `1c9ef36`. Two defensible extractions
yield `7ced3e1b…` and `b56fcb9b…`, sharing no prefix; a range-based `sed` reading
truncates the body at the template's embedded `---` and yields a third answer. The
failure is fail-closed but its symptom is indistinguishable from genuine post-approval
drift, so its practical cost is that humans stop believing mismatch reports.

**Proposed encoding:** Amend `kernel/contract/work-state.md` §"Computing the hash" to
name one normative command whose output *is* the definition of "the plan body"
(`awk 'n>=2{print} /^---$/{n++}' plan.md`), with a stated invariant that any
implementation must reproduce byte-for-byte, plus a one-line note that a range-based
extraction is a known-wrong implementation. Add the divergence case to the contract
validation checklist in `kernel/contract/document-contract.md`. This is independent of
the approval-code proposal and is worth merging even if that one is rejected — it repairs
the existing rule rather than replacing it.
