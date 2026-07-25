---
id: candidates/2026-07-25-effort-as-routing-axis
type: knowledge
layer: project
scope: on-demand
status: candidate
proposed_target: kernel/principles/model-routing
---
**Learning:** The tier model in `model-routing.md` assumes tier selection *is* model
selection — "route to the cheapest qualifier for the tier the task needs" resolves to one
decision. That assumption no longer holds. Reasoning depth is now a first-class request
parameter (`effort`: low / medium / high / xhigh / max) that varies independently of the
model, and the cheap settings are strong: Anthropic's own migration guidance for Opus 5
says to start at `xhigh` for coding and agentic work and then *sweep downward*, because
`low` and `medium` "punch well above their weight" and prior-model defaults rarely
transfer. So a task now has at least two ways to be routed — a smaller model at high
effort, or a larger model at low effort — and the tier vocabulary cannot express the
difference, let alone say which is cheaper.

Two adjacent facts land in the same place. Calibration placements are already declared
per (model × harness × config), so `effort` is silently part of the config key and
belongs in the recorded placement rather than left implicit. And a probe that passes at
`xhigh` says nothing about the same model at `low`, which is the setting a cost-sensitive
project would actually deploy — a tier placement without an effort level is not a
reproducible claim.

**Evidence:** `adapters/claude-code/capabilities.md` carries a tier mapping dated
2026-07-01 whose stated recalibration trigger is "vendor lineup change." The Claude 5
family (Fable 5, Opus 5, Sonnet 5, alongside Haiku 4.5) is that change, so the trigger has
fired and the table is stale on its own terms. The lineup also breaks the tier↔model
correspondence in a second way: Fable 5 sits *above* Opus-class, which the manifest
half-anticipated with the parenthetical "(or above, e.g. Fable-class where available)" —
a hedge in prose where the table needs a row. Meanwhile Sonnet 5 reaches near-Opus quality
on agentic and coding work, so "implementation tier = Sonnet-class" now spans a much wider
capability range than when it was written.

**Proposed encoding:** Amend `kernel/principles/model-routing.md` (kernel minor) so a
routing decision is a **(tier, effort) pair**, not a tier alone; restate the placement rule
as "cheapest qualifying (model, effort) combination" so the two axes are priced together.
Amend `kernel/workflows/model-calibration.md` to require the effort level in every recorded
placement and to state that a probe result is scoped to the effort it ran at. Re-date the
adapter tier table. Explicitly **out of scope**: naming specific models in the kernel — the
tier abstraction exists so the kernel survives lineup changes, and this candidate is an
argument for a second axis, not for hardcoding vendors.
