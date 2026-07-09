---
id: candidates/2026-07-09-packet-execution-records
type: knowledge
layer: project
scope: on-demand
status: candidate
proposed_target: kernel/templates/review-packet
---
**Learning:** The review-packet anchoring rules exclude "the first agent's findings and review synthesis," which also excluded the QA *execution record* — so the external reviewer can't distinguish "QA never ran" from "QA ran but wasn't in my packet." Execution records are facts about what happened, not reviewer conclusions; excluding them risks false-positive findings (harmless-but-noisy), while including them costs no independence.

**Evidence:** First cross-vendor run (Folio, `2026-07-gallery-persist-on-rehydrate`, 2026-07-09): Codex flagged missing manual-QA execution evidence. The finding happened to be genuinely open, so no harm — but had the QA been executed and recorded, the same packet would have produced a false finding.

**Proposed encoding:** Amend `kernel/templates/review-packet.md` anchoring rules: independent-review packets *include* execution records (test runs, QA records — the factual "what ran" portions) while continuing to exclude findings, verdicts, and synthesis narrative.
