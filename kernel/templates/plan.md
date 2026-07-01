---
id: kernel/templates/plan
type: template
layer: kernel
scope: on-plan
requires: []
overridable: true
version: 1
---

# Template: Implementation Plan

Copy into `.folioos/work/<yyyy-mm>-<slug>/plan.md`. Everything below the frontmatter is hashed at approval — see the [work-state contract](../contract/work-state.md).

```markdown
---
id: work/<yyyy-mm>-<slug>
type: plan
status: draft
created: <date>
approved_by:
approved_at:
plan_hash:
---

# <Title>

## Goal
<One paragraph: what exists when this is done, and why it's worth doing. Link the PRD if one exists.>

## Options considered   <!-- include when real alternatives exist -->
- **Option A — <name>:** <approach, tradeoff>
- **Option B — <name>:** <approach, tradeoff>
- **Recommendation:** <which and why>

## Steps
<!-- One block per step. Checkpoint actions (schema changes, deletions, new
     dependencies, outward writes) MUST appear here explicitly — anything
     not named re-gates during execution. -->

### 1. <Step name>
- **Files:** `Path/File.swift` (new | edit | delete)
- **What & why:** <the change and its purpose>
- **Risk:** <none | ⚠️ description — e.g. "touches persistence: data-loss class">
- **Model:** reasoning | implementation | utility · **Effort:** S | M | L

## UI                    <!-- design-affecting plans: mockups or written descriptions -->
<Mockup images, or a written description of each screen/state.>

## Test plan             <!-- a commitment — QA verifies it was executed -->
- <unit tests to write, scenarios to run, system-feature checklist if applicable>

## Risks & open questions
- <risks that might reshape this plan — surface BEFORE approval>
- <questions needing the human's answer>

## Out of scope
- <nearby things deliberately not done>

## Re-gate log           <!-- empty at approval; appended during execution -->
```
