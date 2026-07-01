---
id: kernel/workflows/ux-review
type: workflow
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Workflow: UX Review

Runs inside the review pipeline whenever a change touches user-facing UI.

1. **Capture evidence.** Screenshots of every changed screen: light + dark, default + largest Dynamic Type, empty + populated states. Store in `evidence/`.
2. **HIG pass.** hig-expert persona reviews the captures against the pack's HIG checklist — visuals only, no diff.
3. **Flow pass.** Walk the user journey end-to-end (`run-app` if available): entry point, happy path, error states, exit. Note dead ends and surprise modals.
4. **Accessibility pass.** Apply [standards/accessibility.md](../standards/accessibility.md): VoiceOver walk, Dynamic Type, contrast, targets.
5. **Findings** join the review pipeline's verify + synthesis stages.

Design-heavy features should have had mockups at *plan* time — a UX review that redesigns the feature means the plan skipped that step; note it as process feedback for the DX engineer.

## Capability ladder
- **Preferred** (requires: run-app, screenshots): as written.
- **Fallback** (requires: screenshots): static captures only; flow pass becomes a written walkthrough for the human to perform.
- **Floor** (requires: file-system): review view code as a UI description; output a manual visual-QA script. Never sign off visuals unseen — the report must say "visual verification pending human QA."
