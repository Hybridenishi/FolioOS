---
id: kernel/standards/testing-policy
type: standard
layer: kernel
scope: always
requires: []
overridable: true
version: 1
---

# Testing Policy

Tests exist to prevent the headache class: a new feature or fix breaking something unforeseen. Coverage follows *risk*, not a percentage.

## Requirements by change type

| Change | Bar |
|---|---|
| Logic-bearing code (parsing, calculation, state transitions, persistence) | Unit tests **required**, including edge cases the plan identified |
| Bug fix | A test that fails without the fix, **required, no exceptions** |
| **System features** — import/export, backup/restore, migrations, sync | Scenario QA **required** per [checklists/system-feature-qa.md](../checklists/system-feature-qa.md), with evidence recorded; unit tests alone never pass these |
| Pure UI composition | Case-by-case; prefer making logic testable by extracting it out of views |
| Generated docs, config, mechanical renames | None beyond compile/build passing |

## Rules

- The plan's test plan section is a commitment, not a suggestion — QA verifies it was executed.
- Tests assert behavior, not implementation; a refactor that changes no behavior should break no tests.
- Flaky tests are bugs: fix or delete within the change that exposes them, never `skip` and move on.
- Test data includes hostile shapes: empty, enormous, malformed, wrong-version, interrupted-midway.
