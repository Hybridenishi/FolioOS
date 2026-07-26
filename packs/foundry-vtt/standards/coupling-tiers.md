---
id: packs/foundry-vtt/standards/coupling-tiers
type: standard
layer: pack:foundry-vtt
scope: always
requires: []
overridable: true
version: 1
---

# Standard: Coupling Tiers

**Policy: prefer the layer with the longest maintenance horizon.** Foundry worlds run a stack of core, one game system, and a dozen or more community modules, each on its own release schedule. What you couple to determines how often your project breaks for reasons that have nothing to do with your code.

| Tier | What | Use |
|---|---|---|
| **A** | Foundry core and game-system APIs | **Preferred for everything.** Ships with the platform the project already pins. |
| **B** | Documented module hooks and public module APIs | Optional, and late. One adapter per module, capability-probed at startup, pinned to a known-good version range, degrading with an explicit error rather than crashing. |
| **C** | Module internals, undocumented backends, private fields | **Never.** No exceptions for popular modules. |

## Rules

- **Tier A is the default, not the fallback.** A Tier B path needs a written reason why Tier A cannot do the job.
- **One adapter per Tier B module.** Not a shared compatibility layer — modules fail independently, so they degrade independently.
- **Every Tier B integration probes at startup** and reports its verdict. A module that is absent, or present at an unrecognized version, produces an explicit degraded error. It does not crash, and it does not silently change behavior.
- **Tier B is for interpreting and reporting**, not for calling. Read what a module did; don't drive it.
- **Reaching into Tier C is a defect regardless of whether it works.** It works until the module's next patch release.

## The rule this standard exists to prevent people from misreading

**Writing to a world where automation modules are active is not module coupling.** Calling the Tier A API is precisely what lets those modules' hooks fire correctly. Writing *around* them — direct document mutation that skips the system's own path — is the unsafe option, because it desynchronizes state the modules believe they own.

So "avoid module coupling" never means "avoid worlds with modules," and it never justifies a lower-level write to sidestep automation. It means: call the documented API, let the ecosystem react, and report what happened.

## Consequence for planning

A plan step that touches a module surface names its tier in the step body. Tier B steps additionally name the version range and the degraded behavior. A step that would reach Tier C re-gates — it is a scope change, not an implementation detail.
