# Project Overrides

Project-layer deltas against kernel/pack documents — `type: override`, per the [document contract](../../../..//kernel/contract/document-contract.md). An override without a `rationale` is invalid. Recompile the adapter artifacts after adding or changing one.

Example — `testing-policy.md` in this directory:

```markdown
---
id: project/overrides/testing-policy
type: override
layer: project
target: kernel/standards/testing-policy
operation: extend
rationale: "This app's export format is consumed by third parties; format changes need contract tests."
scope: always
requires: []
overridable: true
version: 1
---

In this project, additionally: any change to the export format runs the
format contract-test suite, and the format version constant must bump.
```

Smell check: if every project ends up overriding the same document the same way, the kernel is wrong — file a candidate instead of copy-pasting the override into the next project.
