---
id: kernel/templates/qa-run
type: template
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Template: QA Run Record

Append-only evidence: `evidence/<yyyy-mm-dd>-qa.md`. Honesty rule: **not-run is a status, not a gap to hide.** An unfilled or partial record reads as QA-not-done at the merge gate — which is the correct signal.

```markdown
# QA Run — <yyyy-mm-dd>

- **Work:** <slug> · **Build:** <commit/version> · **Environment:** <simulator/device, OS>
- **Executed by:** <agent (which) | human>

## Test plan execution        <!-- every item from the plan's test plan -->
| Item | Result | Notes |
|---|---|---|
| <item> | pass / fail / not-run | <output, or why not run> |

## System-feature scenarios   <!-- if applicable; from system-feature-qa checklist -->
| Scenario | Data shape | Result | Notes |
|---|---|---|---|
| <e.g. restore from backup> | <e.g. 10k-entry archive, older schema> | pass / fail | |

## Regression spot-checks
| Neighboring behavior | Result |
|---|---|

## Failures & follow-ups
<Each failure: reproduction, severity, routed to fix-in-scope or re-gate.>
```
