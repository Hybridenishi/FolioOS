---
id: kernel/templates/rfc
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Template: RFC

For proposals bigger than one plan — architecture shifts, methodology changes, multi-feature directions. An RFC explores; an ADR records what was decided; plans execute it. Lives in `.folioos/work/<yyyy-mm>-rfc-<slug>/rfc.md` (or FolioOS's own repo for methodology RFCs).

```markdown
# RFC: <Title>

- **Date:** <yyyy-mm-dd>
- **Status:** draft | under-discussion | accepted | declined

## Summary
<Three sentences max.>

## Motivation
<Why now. What breaks or stagnates if we don't.>

## Proposal
<The design, at whatever depth the decision needs.>

## Alternatives
- **Do nothing:** <the honest cost of that>
- **<Alternative>:** <tradeoffs>

## Risks & unresolved questions
- <what could make this wrong>

## Adoption path
<How we get there incrementally; what sequence of plans this becomes.>
```
