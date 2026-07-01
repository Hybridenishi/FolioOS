---
id: kernel/templates/adr
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Template: Architecture Decision Record

One decision per file: `.folioos/decisions/<nnnn>-<slug>.md`, sequentially numbered, append-only. ADRs are never edited to change history — a reversed decision gets a *new* ADR that supersedes the old one.

Write an ADR whenever a change decides something a future maintainer would otherwise have to reverse-engineer: architecture choices, dependency adoptions, data-format commitments, deliberate standard deviations. The architect persona's "silent decisions" list is a direct feed.

```markdown
# ADR-<nnnn>: <Decision as a short assertive phrase>

- **Date:** <yyyy-mm-dd>
- **Status:** accepted | superseded by ADR-<nnnn>
- **Work:** <link to the work directory that produced this>

## Context
<The forces: what problem, what constraints, what mattered.>

## Decision
<What we chose. Assertive, one paragraph.>

## Alternatives considered
- **<Alternative>:** <why not>

## Consequences
<What gets easier, what gets harder, what we've committed to. Include the
revisit trigger if there is one: "revisit when/if <condition>.">
```
