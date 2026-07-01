---
id: kernel/checklists/pre-merge
type: checklist
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Checklist: Pre-Merge

Self-applied at the end of build (stage 5 of the feature lifecycle) and re-verified at the merge gate.

## Work-state
- [ ] Plan status is `executing`, hash still matches the approved content
- [ ] Every plan step done, or its deviation recorded in the re-gate log
- [ ] No checkpoint actions occurred that the plan didn't name

## Code
- [ ] Builds clean — no new warnings introduced
- [ ] No dead code, commented-out blocks, debug prints, or stray TODOs without owners
- [ ] No secrets, keys, or user data in the diff
- [ ] Diff contains only the plan's scope (no ride-along refactors)

## Tests
- [ ] Plan's test plan fully executed; results in the QA record
- [ ] Bug fixes include their fails-without-the-fix test
- [ ] Full suite passes — not just the new tests

## Evidence & docs
- [ ] Review synthesis in `evidence/`
- [ ] QA record in `evidence/` (complete, or honestly marked partial)
- [ ] Technical writer's regeneration worklist executed; `generated:` dates updated
- [ ] Silent decisions from the architect's list captured as ADRs
- [ ] Durable learnings captured to `candidates/`
