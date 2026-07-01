---
id: kernel/principles/autonomy-model
type: principle
layer: kernel
scope: always
requires: []
overridable: false
version: 1
---

# Autonomy Model: Plan → Approve → Execute

The default contract for all non-trivial work. Mechanically enforced by the [work-state contract](../contract/work-state.md); this document defines the policy.

## The contract

1. **Plan.** For any non-trivial task, the agent produces a plan (per [templates/plan.md](../templates/plan.md)) in `.folioos/work/<yyyy-mm>-<slug>/plan.md`, `status: draft`, and **stops**. Risks called out in the plan are meant to reshape it *before* approval — a plan that hides its risks to get approved violates the working agreement.
2. **Approve.** The human reads, edits or requests changes, then sets `status: approved` with the content hash. Only humans approve.
3. **Execute.** The agent executes the approved plan end-to-end without re-litigating settled decisions, checking the hash first.

## What counts as trivial (no plan needed)

Typo-level fixes, single-file mechanical changes with no behavioral risk, doc regeneration, answering questions. When in doubt, it isn't trivial.

## Checkpoint actions — always re-gate, even mid-approved-plan

These require the plan to have *explicitly named* them; discovering one mid-execution triggers the re-gate rule (`status: blocked`, stop):

- Schema or persistence-format changes (migrations, store model edits)
- Deleting or rewriting code beyond the plan's named files
- Adding a third-party dependency
- **Any outward-facing write**: creating PRs, pushing, publishing releases, writing to Notion/GitHub/any external service. Having an `mcp:*` capability is not authorization to use it.
- Destructive or hard-to-reverse operations of any kind

## Interaction with agent-native plan modes

If the agent has `plan-mode`, use it for the *conversation*, but the artifact of record is still the work-state file. Chat approval without the file is not approval under this contract.
