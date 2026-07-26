---
id: kernel/templates/plan
type: template
layer: kernel
scope: on-plan
requires: []
overridable: true
version: 2
---

# Template: Implementation Plan

Copy into `.folioos/work/<yyyy-mm>-<slug>/plan.md`. Everything below the frontmatter is hashed at approval — see the [work-state contract](../contract/work-state.md). The agent fills `body_hash` and issues an approval card; the human fills the other four fields.

```markdown
---
id: work/<yyyy-mm>-<slug>
type: plan
status: draft
created: <date>
body_hash:
approved_by:
approved_at:
approval_code:
---

# <Title>

## Goal
<One paragraph: what exists when this is done, and why it's worth doing. Link the PRD if one exists.>

## Approving this plan approves:   <!-- REQUIRED when the plan carries a policy or safety decision -->
<!-- Each numbered item is something a "yes" commits to that is NOT obvious from the
     step list: a contract loosened, a constraint accepted, a boundary moved, a second
     repository written to. If the plan carries no such decision, omit this section —
     do not pad it with restated steps. -->
1. <the commitment, and what it costs>

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
- **Depends on:** <none | step N — so parallel and blocking work is readable at a glance>
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

## On finishing a draft

Compute the body digest and end the response with the approval card — every time the plan
enters or re-enters `draft`, including after an edit to an already-approved plan (which
voids the approval under work-state rule 7):

```sh
awk 'n>=2{print} /^---$/{n++}' plan.md | shasum -a 256
```

Prose tables and rule sets beat prose paragraphs at the approval gate, and a real
executive summary beats both. A derived `plan.view.html` is *not* part of this template:
`plan.md` is what gets hashed and executed, so any HTML is a view for the approver only,
and it earns its authoring cost rarely. See `.folioos/candidates/2026-07-12-html-approval-views.md`.
