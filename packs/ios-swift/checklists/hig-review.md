---
id: packs/ios-swift/checklists/hig-review
type: checklist
layer: pack:ios-swift
scope: on-review
requires: [screenshots]
overridable: true
version: 1
---

# Checklist: HIG Review

Applied by the hig-expert persona to visual evidence (screenshots / running app). Companion: [knowledge/hig-cheatsheet.md](../knowledge/hig-cheatsheet.md).

## Navigation & structure
- [ ] Navigation pattern matches content shape (hierarchy → push; self-contained task → sheet; destructive/branching choice → confirmation dialog)
- [ ] Sheets are dismissible the standard ways; no trapped modals
- [ ] Titles say where you are; back buttons say where you came from
- [ ] Toolbar/actions placement follows platform conventions (primary action top-trailing or prominent, destructive isolated)

## Controls & interaction
- [ ] Standard controls used where a standard control exists; every custom control has a justification on file
- [ ] Touch targets ≥ 44×44pt; adequate spacing between adjacent tappables
- [ ] Swipe actions, pull-to-refresh, context menus used where users expect them
- [ ] Destructive actions: confirmation + undo where feasible; never a default/primary style

## Presentation
- [ ] Legible at largest accessibility Dynamic Type without clipped/truncated essentials
- [ ] Both light and dark appearance verified in evidence
- [ ] Feedback for every action: state changes are visible; long operations show progress; haptics restrained and semantic
- [ ] Copy is sentence-style, concise, verb-first on buttons; no developer vocabulary leaking to users

## Verdict discipline
- [ ] Every finding cites the HIG principle at stake and the smallest resolving change
- [ ] Anything not visible in evidence is listed as "needs human visual QA", not assumed fine
