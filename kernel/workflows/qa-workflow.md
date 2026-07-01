---
id: kernel/workflows/qa-workflow
type: workflow
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Workflow: QA

Risk-scaled verification that the built thing works — beyond the unit tests written during build.

1. **Scope the risk.** From the plan: what does this change touch, and what *shares code* with what it touches? The second list is where regressions live.
2. **Execute the plan's test plan.** Every item, with results recorded — the plan committed to it at approval.
3. **System-feature scenarios.** If the change involves import/export, backup/restore, migration, or sync: run [checklists/system-feature-qa.md](../checklists/system-feature-qa.md) in full, with real and hostile data shapes. Unit tests never satisfy this row of the testing policy.
4. **Regression spot-check.** Exercise the top 3 neighboring behaviors from step 1.
5. **Record.** QA-run record per [templates/qa-run.md](../templates/qa-run.md) into `evidence/` — including failures and *not-run* items. An honest partial record beats a complete-looking one.

Failures route back to build (small fixes within plan scope) or to re-gate (anything revealing the plan was wrong).

## Capability ladder
- **Preferred** (requires: run-tests, run-app): as written.
- **Fallback** (requires: run-tests): automated suite runs; app-level scenarios become a precise manual script with expected results per step, executed by the human, results pasted into the QA record.
- **Floor** (requires: file-system): produce the full manual QA script and an *empty* QA record for the human to fill. The merge gate treats an unfilled record as QA-not-done.
