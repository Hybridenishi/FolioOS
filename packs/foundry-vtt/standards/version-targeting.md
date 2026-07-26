---
id: packs/foundry-vtt/standards/version-targeting
type: standard
layer: pack:foundry-vtt
scope: on-plan
requires: []
overridable: true
version: 1
---

# Standard: Version Targeting

**Policy: pin one Foundry build and one system version. Support that combination and say so.** The Foundry ecosystem has no stable API contract across majors, and a project that claims to support "Foundry v13+" is claiming something it has not tested.

The pinned combination is named in the project's own documentation, not here — this document says *how* to pin, and the numbers live where they can be updated without a pack bump.

## Rules

- **One supported combination at a time**, stated as exact versions: Foundry major + build, system major.minor.patch. "Latest" is not a version.
- **Report the running versions at startup** and compare them against the pin. A world running something else gets an explicit warning, not silent best-effort.
- **Version-specific workarounds carry their version in a comment.** A workaround whose trigger condition is undocumented outlives the bug it was for, and then nobody dares delete it.
- **An upgrade is a unit of work, not a side effect.** New Foundry build or system minor → its own plan, its own re-verification of `knowledge/`, its own smoke run.
- **Publish the compatibility matrix.** Tested combinations, with dates. An untested combination is absent from the matrix, never listed hopefully.
- **Content-provenance is a version axis too.** Where a system ships more than one rules edition and a world holds documents from several, read each document's own provenance rather than assuming a world-wide mode. Deriving the edition from a world setting is the bug this rule exists to prevent.

## Deleting rather than annotating

When an upgrade invalidates a knowledge entry, **delete it**. Do not annotate it with "(pre-v14 only)".

The reasoning is the same as `packs/ios-swift`: an annotated entry still costs a reader's attention on every pass, still shows up in search, and still invites someone to apply it. The pack is a picture of the supported combination, and git holds the history of every combination before it. A pack that accumulates version caveats stops being read.
