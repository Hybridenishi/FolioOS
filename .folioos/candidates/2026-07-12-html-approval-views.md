---
id: candidates/2026-07-12-html-approval-views
type: knowledge
layer: project
scope: on-demand
status: candidate
proposed_target: kernel/templates/plan
---
**Learning:** For most plans, the comprehension benefit attributed to "HTML documentation" comes from information design that Markdown can host directly (a real executive summary, rule sets as tables, an explicit "approving this approves…" block, a dependency line per step). A derived HTML view earns its ~2× authoring/token cost only at the human approval gate, and only for plans whose structure can't be held as an ordered list.

**Evidence:** 2026-07-12 evaluation session: research review (NN/g scanning/accordion/aesthetic-usability findings; Mayer multimedia principle; weak/mixed UML-comprehension literature; 2–3× HTML token overhead) plus a practical rebuild of `Folio:work/2026-07-title-cleanup-review-ux` as a designed HTML view. ~70% of the observed improvement was re-editing the information, not the medium; the HTML-only wins were the dependency-graph and write-boundary SVGs, the persistent TOC, and peripheral risk severity. The work-state contract already settles canonicity: `plan.md` is hashed, appended-to, and executed — HTML can only be a derived view.

**Proposed encoding:**
1. Amend `kernel/templates/plan.md` (benefits every plan, no HTML involved): require an "Approving this plan approves:" block when a plan carries a policy/safety decision; prefer tables for rule sets; each step names what it depends on.
2. New optional practice: at approval time, generate `plan.view.html` beside `plan.md` — stamped with the `plan_hash` it renders, regenerated on re-approval, void on hash mismatch (mirrors the execute-only-on-hash-match rule). For the human approver only; executors never read it.
3. **Trigger rule — generate a view only when BOTH hold:** (a) non-linear execution structure (parallel tracks, convergence points — anything beyond an ordered list); (b) at least one policy or safety decision approval consciously accepts. Line count alone never triggers. Of the 14 Folio plans existing 2026-07-12, only `title-cleanup-review-ux` and arguably `merge-workspace-manual-combine` qualify.
