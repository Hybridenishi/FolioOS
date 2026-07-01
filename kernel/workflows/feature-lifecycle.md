---
id: kernel/workflows/feature-lifecycle
type: workflow
layer: kernel
scope: on-plan
requires: []
overridable: true
version: 1
---

# Workflow: Feature Lifecycle

The end-to-end path for a unit of work. Other workflows plug into its stages.

```
intake → (PRD) → plan → ⛔ APPROVAL → build → self-check → review pipeline
      → QA → docs → ⛔ MERGE GATE → merge → release train
```

1. **Intake.** Clarify the goal. If the work is product-shaped (user-visible behavior with choices to make), draft a PRD ([templates/prd.md](../templates/prd.md)) first; otherwise the plan's goal section suffices.
2. **Plan** *(reasoning tier)*. Write the plan per [templates/plan.md](../templates/plan.md) into a new work-state directory. Design-heavy work includes mockups or written UI descriptions. Offer path options where real alternatives exist.
3. **⛔ Approval gate.** Human approves per the [work-state contract](../contract/work-state.md). No build before hash-recorded approval.
4. **Build** *(implementation tier)*. Branch per [git-workflow](../standards/git-workflow.md); execute the approved plan step-by-step; re-gate on any checkpoint action not named in the plan.
5. **Self-check.** Before requesting review: build passes, test plan executed, [checklists/pre-merge.md](../checklists/pre-merge.md) self-applied. Self-check is a filter, not a substitute for review.
6. **Review.** Run [review-pipeline.md](review-pipeline.md); UI changes also trigger [ux-review.md](ux-review.md).
7. **QA.** Run [qa-workflow.md](qa-workflow.md); evidence to `evidence/`.
8. **Docs** *(utility tier)*. Execute the technical writer's regeneration worklist.
9. **⛔ Merge gate.** Human reviews the synthesized report + evidence, merges the PR. Set plan `status: done`.
10. **Release train.** Merged work rides the next release ([release.md](release.md)).

## Capability ladder
- **Preferred** (requires: shell, run-tests): as written.
- **Floor** (requires: file-system): stages that need execution (build, test, QA) become precise instructions for the human to run, with results pasted back into evidence. The gates and artifacts are unchanged.
