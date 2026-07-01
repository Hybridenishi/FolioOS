---
id: kernel/templates/prd
type: template
layer: kernel
scope: on-plan
requires: []
overridable: true
version: 1
---

# Template: PRD

For product-shaped work — user-visible behavior with choices to make. Lives beside the plan in the work directory as `prd.md`. Kept short: a PRD nobody rereads is ceremony.

```markdown
# PRD: <Feature>

## Problem
<Who hurts, how, today. One paragraph.>

## Outcome
<The user-observable difference when this ships. Not implementation.>

## Acceptance criteria      <!-- the PM persona reviews against exactly these -->
- [ ] <verifiable statement of behavior>
- [ ] <empty state: what a first-run/no-data user sees>
- [ ] <error states: what failure looks like to the user>

## Non-goals
- <adjacent things this deliberately does not do>

## Design notes
<Mockups, references, HIG patterns to follow. Or "standard platform patterns, no custom UI.">

## Open product questions
- <decisions the human must make before planning>
```
