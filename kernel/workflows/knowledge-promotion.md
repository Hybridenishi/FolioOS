---
id: kernel/workflows/knowledge-promotion
type: workflow
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Workflow: Knowledge Promotion

The upward loop — how project discoveries become methodology. Without this, Userland gets smarter while the kernel fossilizes, and FolioOS becomes a framework *worked around* rather than used.

## Capturing candidates (continuous, any agent)

When work surfaces a durable learning — a platform pitfall, a pattern that kept a bug out, a checklist gap revealed by a recurring review finding, a better-idea-mid-execution that the re-gate rule deferred — write it to `.folioos/candidates/<yyyy-mm-dd>-<slug>.md`:

```markdown
---
id: candidates/2026-07-03-swiftdata-migration-ordering
type: knowledge
layer: project
scope: on-demand
status: candidate
proposed_target: pack:ios-swift/knowledge/common-pitfalls
---
**Learning:** <one paragraph — the fact>
**Evidence:** <what happened; link the work dir, review finding, or failing test>
**Proposed encoding:** <new pitfall entry | checklist item | standard amendment>
```

Capture is cheap and unilateral. Promotion is neither.

## Triage (periodic — monthly, or when candidates ≥ 5)

1. **DX engineer persona** reviews all candidates against: Is it durable (survives the next OS cycle)? Is it general (this project only → keep as a project override instead)? Is there evidence, not just opinion? Which layer does it belong to (pack knowledge vs. checklist item vs. kernel standard)?
2. Produces a triage report: per candidate — **promote** (with drafted edit to the target document), **demote to project override**, or **reject** (with reason).
3. **⛔ Human gate.** The human is the editor-in-chief of their own methodology — every promotion is a deliberate merge into FolioOS, with the appropriate version bump and changelog entry. No agent edits the kernel or packs autonomously.
4. Promoted/rejected candidates move to `candidates/archive/` with their outcome noted, so rejected ideas don't resurface annually as new.

## Capability ladder
- **Floor** (requires: file-system): the whole workflow is file-based by design.
