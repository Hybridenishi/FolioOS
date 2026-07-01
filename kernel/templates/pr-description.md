---
id: kernel/templates/pr-description
type: template
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Template: PR Description

```markdown
## What
<One paragraph: the change, in behavior terms.>

## Why
<Link the work directory: `.folioos/work/<yyyy-mm>-<slug>/`. The plan is the
authority; don't duplicate it — summarize in two sentences.>

## Review evidence
- Review synthesis: `evidence/<date>-review.md`
- QA record: `evidence/<date>-qa.md`
- Screenshots: <for UI changes — before/after, light/dark>

## Risk notes
<Checkpoint actions this PR contains (migrations, dependencies, deletions)
and how each was verified. "None" is a valid, explicit answer.>

## Docs
<Regenerated pages, or "no docs affected" per the technical writer's pass.>
```
