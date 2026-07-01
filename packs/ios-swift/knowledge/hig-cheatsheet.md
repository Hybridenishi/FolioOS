---
id: packs/ios-swift/knowledge/hig-cheatsheet
type: knowledge
layer: pack:ios-swift
scope: on-demand
requires: []
overridable: true
version: 1
---

# Knowledge: HIG Cheatsheet

Durable HIG principles for fast recall. This is a *cheatsheet*, not the source of truth — when a specific ruling matters, verify against the current HIG (`web` capability), because Apple revises it every cycle. Entries here are chosen for having survived many cycles.

## Choosing a container
| Situation | Reach for |
|---|---|
| Navigating a content hierarchy | `NavigationStack` push |
| Self-contained task with its own Done/Cancel | Sheet |
| Quick branching or destructive choice | Confirmation dialog |
| Rare, blocking, must-acknowledge information | Alert (sparingly — alerts are for interruptions that earn it) |
| Peek at secondary detail | Popover (iPad/Mac) / sheet with detents (iPhone) |

## Numbers that matter
- **44×44pt** minimum touch target.
- Standard margins over hand-tuned ones — layout guides exist so screens agree with each other.
- One screen, one primary action. If everything is prominent, nothing is.

## Text & type
- Dynamic Type via text styles (`.body`, `.headline`…), never fixed sizes for reading text.
- Buttons: verbs ("Save", "Import"), not "OK" when a verb exists. Title-style for nav titles, sentence-style for body copy.
- Never blame the user in error copy; say what happened and what they can do.

## Behavior
- Respond to every touch — visible state change or motion within perceptual immediacy; show progress past ~1s.
- Undo beats confirmation for reversible actions; confirmation is for the irreversible.
- Haptics are semantic punctuation (success, warning, selection), not decoration.
- Dark mode, Dynamic Type, and Reduce Motion are *inputs to design*, not QA afterthoughts.

## Platform feel (the un-checklistable rule)
When in doubt, do what Apple's own apps do for the equivalent interaction. Users' muscle memory is the spec.
