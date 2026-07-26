---
id: packs/foundry-vtt/checklists/visibility-review
type: checklist
layer: pack:foundry-vtt
scope: on-review
requires: []
overridable: true
version: 1
---

# Checklist: Visibility Review

Applied to any change that writes player-visible or GM-only content — journals, notes, chat, lore, anything with an ownership map.

This is the highest-consequence write class in a Foundry project, and **not for rules reasons**: a wrong hit point value is corrected in seconds; GM notes rendered visible to a player cannot be un-seen. Errors here are irreversible in the only sense that matters, because the damage is to a person's experience of the game rather than to data.

## Gate

- [ ] **Visibility is a required input, and the default is GM-only.** No call may write content whose audience was not stated. A default of "inherit" is not a default, it is a coin toss.
- [ ] **One call, one audience.** A single write does not produce mixed-visibility content. If a piece of content has parts with different audiences, the caller declares which bucket each part belongs to, and they are separate writes.
- [ ] **Page-level ownership is set explicitly** where content is nested. Inheriting from a parent is the likeliest way notes leak — the parent's audience was correct when it was created and then somebody widened it.
- [ ] **Names are resolved and reported, not IDs.** A receipt reading "visible to Alice and Bob" can be checked at a glance; an ownership map keyed by user id cannot, and an unverifiable receipt on a spoiler-sensitive write is worth nothing.
- [ ] **Ambiguous or unmatched names refuse the write.** Never guess which player was meant, and never fall back to a broader audience because a name did not resolve. Failing loudly is the safe direction.
- [ ] **Links are maintained in both directions** where content references a document, and stored in a field the content pipeline does not overwrite. Descriptive fields that importers rewrite are the wrong home for a link.
- [ ] **Preview and apply gating**, per [mutation-pattern](../standards/mutation-pattern.md) — including the read-back receipt, which for this class means reading back *who can see it*, not just that it exists.

## Review question that catches the rest

For each piece of content this change can write, ask: **if this ended up visible to the wrong person, would we find out?** If the answer is "only if a player mentions it," the receipt is inadequate regardless of whether the ownership logic is correct.
