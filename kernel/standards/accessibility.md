---
id: kernel/standards/accessibility
type: standard
layer: kernel
scope: on-review
requires: []
overridable: true
version: 1
---

# Accessibility Standard

Baseline bar for every user-facing change — not a launch-week retrofit.

- Every interactive element has an accessible label that says what it *does*, not what it looks like ("Add entry", not "Plus button").
- Text scales: layouts survive the largest Dynamic Type sizes without truncating essential content or breaking interaction.
- Contrast meets platform guidance in both light and dark appearance.
- Touch/click targets meet platform minimums (44pt on Apple platforms).
- Meaning is never carried by color alone.
- Screen-reader pass for new screens: navigate the flow end-to-end with VoiceOver (or platform equivalent); element order must make sense aurally.
- Motion effects respect the reduce-motion setting.

The UX review workflow enforces this via the pack's review checklists; findings here are `should-fix` minimum, `blocker` when a flow is unusable.
