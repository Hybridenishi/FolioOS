---
id: packs/foundry-vtt/pack
type: knowledge
layer: pack:foundry-vtt
scope: on-demand
requires: []
overridable: false
version: 1
---

# Pack: foundry-vtt

Platform knowledge for building against **Foundry Virtual Tabletop** — modules that run inside the Foundry client, and services that drive a Foundry world from outside it. Layered on the kernel by the cascade; adds standards, a checklist, and knowledge. Overrides nothing in the kernel at this version.

Scope is the **platform**, not a game system. Foundry's own concepts — documents, the socket protocol, module manifests, ownership, compendia — are reusable across systems; a game system's data model is not. D&D 5e specifics (AC formula evaluation, exhaustion's 2014-vs-2024 split, the Activity model) belong in the project layer, and the reference project draws the same line: "Foundry-level concepts remain reusable internally, but the public MCP tools speak D&D 5e."

**Positions this pack takes** (each is an ADR-able default — projects deviate via override + ADR, not silently):

- **Prefer the layer with the longest maintenance horizon.** Core and system APIs by default; documented module APIs only behind a capability probe; module internals never — [standards/coupling-tiers.md](standards/coupling-tiers.md)
- **Every mutation is preview → scoped single-use token → system API → receipt read back from the changed document** — [standards/mutation-pattern.md](standards/mutation-pattern.md)
- **Pin one Foundry build and one system version**; the compatibility matrix is a published artifact, not folklore — [standards/version-targeting.md](standards/version-targeting.md)
- **A deployable artifact's inputs are derivable from the repository alone**, never from files left on a host — [standards/deployment-integrity.md](standards/deployment-integrity.md)

**Maintenance rhythm:** this pack turns over on *version* events, not on a calendar. Each Foundry major build and each system minor: re-verify the knowledge files against the running world, revisit version-targeting, bump the pack minor. Knowledge entries that a Foundry or system release invalidates are **deleted, not annotated** — same discipline as `packs/ios-swift`, and the reason that pack stays short.

**A note on where the knowledge came from.** Every entry in `knowledge/` was paid for once, in a live world, by something breaking. They are transcribed from a governed project's own findings rather than from documentation, which is why several contradict what a reasonable reading of the API would suggest.

Contents: `standards/` (4) · `checklists/` (visibility-review) · `knowledge/` (common-pitfalls, api-surfaces).
