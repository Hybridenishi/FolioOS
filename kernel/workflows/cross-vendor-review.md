---
id: kernel/workflows/cross-vendor-review
type: workflow
layer: kernel
scope: on-review
requires: []
overridable: true
version: 2
---

# Workflow: Cross-Vendor Review

A second AI opinion from a **different vendor's model** — decorrelation at the training level, the axis neither evidence isolation nor adversarial verify can reach. A different lab's model has different baked-in blind spots and no self-preference bias toward the generating model's output.

**Scope discipline:** checkpoint-class work only — migrations, schema or published-contract changes, system features, release candidates, and high-stakes plans. Running it on every PR is cost without signal; the whole point of tiers is matching scrutiny to risk. Even where [review-pipeline.md](review-pipeline.md) already runs routine persona reviews across multiple providers, checkpoint-class work keeps this formal unanchored packet and verbatim evidence record.

## Prerequisite

The external reviewer should be calibrated before its opinions carry full weight: the target gate is passing [R3 adversarial-verify](../calibration/reasoning-03-adversarial-verify.md) (can it refute bogus findings?) — a reviewer that confirms everything is noise with authority. Projects using specialized languages or ecosystems should also sanity-check domain reading fluency (for example, applying I2's fluency criteria as a reading test). If a newly available live provider has only `provisional` placement per [routing-discovery.md](routing-discovery.md), flag that status explicitly in the report header and prioritize calibrating it on R3 rather than silently dropping Vendor-Split.

## Modes

- **Independent review** (of a plan or a diff): the packet contains the work and its context but **excludes the first agent's findings and reasoning** — anchoring a second opinion on the first defeats the purpose. Question: "what's wrong or missing?"
- **Findings verification**: the packet includes the first agent's findings (but not its synthesis narrative). Question: "refute or confirm each, with mechanism or scenario."

## Stages

1. **Prepare the packet** per [templates/review-packet.md](../templates/review-packet.md). Self-containment is the contract: the external reviewer has no session context, no access to prior discussion, and no ability to ask — a sloppy packet returns confident garbage.
2. **Run the reviewer** using either the vendor adapter's reviewer procedure (e.g., `adapters/codex/reviewer.md`) or an isolated zero-history `multi-provider` dispatch via the active orchestrator slice, always using the procedure's fixed prompt — not an improvised one; prompt drift makes runs incomparable.
3. **File the report** to `evidence/<date>-crossvendor-<vendor>.md`, append-only, verbatim — the receiving agent may annotate *around* it, never edit it.
4. **Surface the deltas.** The synthesis classifies: **agreements** (low information — overlapping training data means shared conclusions aren't ground truth), **cross-vendor deltas** (the product — findings one side raised that the other didn't, or opposite verdicts). Deltas are surfaced to the human explicitly flagged; the synthesizer never silently adjudicates a cross-vendor disagreement.
5. **Human adjudicates** deltas at the normal gate.

## Adoption experiment (built into the workflow)

The first several runs *are* an experiment, tracked in the report footer: **"Did any cross-vendor delta change a decision?"** After ~5 checkpoint-class runs, tally it. Deltas that never changed a decision mean the second set of eyes is ceremony for this project — record that as a candidate and stop running it. One caught migration-class miss pays for years of packets. (This is ADR-0006's philosophy applied to process: keep only what earns its maintenance.)

## Capability ladder
- **Preferred** (`requires: [multi-provider]`): the Lead prepares the unanchored packet/context, dispatches an isolated zero-history cross-vendor turn with the fixed prompt, and files the verbatim report.
- **Standard** (`requires: [shell]`): the session agent prepares the packet and invokes the external CLI directly, then files the report.
- **Floor** (`requires: [file-system]`): the agent prepares the packet and the exact command/prompt; the human shuttles it through the external tool and pastes the report back. The floor *is* the manual experiment — no integration needed to start.
