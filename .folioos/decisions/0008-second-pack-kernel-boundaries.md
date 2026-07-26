# ADR-0008: What a second pack revealed about three kernel boundaries

- **Date:** 2026-07-26
- **Status:** accepted
- **Work:** [.folioos/work/2026-07-foundry-pack/](../work/2026-07-foundry-pack/plan.md) (steps 3–5)

## Context

`packs/foundry-vtt` is the kernel's second pack, and the first one written after the
kernel existed rather than alongside it. Drafting it against `foundryvtt-mcp` — a
distributed system (stdio MCP server, Dockerized sidecar, browser-resident module) verified
against a human-operated live world — surfaced three places where the kernel's contracts had
only ever been exercised by one shape of project: `ios-swift`, where verification is always
a local, agent-launchable simulator.

## Decision

Close all three gaps in the kernel rather than working around them in the pack:

1. **Capability vocabulary gains `live-environment`** — an externally-hosted, human-operated
   instance of the system under test, whose availability the agent cannot establish and must
   probe. **Capability-model rule 1 gains a narrow, named exception**: a workflow requiring
   `live-environment` may declare a floor that produces a verification artifact (script +
   expected receipt) and stops, rather than a file-system-only floor that verifies nothing.
   Every other capability keeps the original rule unchanged — a file-system-only rung stays
   mandatory, because "read the code and reason about it by hand" is always expressible for
   everything except a live, human-operated dependency.
2. **Packs may contribute personas.** The document contract's cascade already added "all
   non-override pack documents (new `id`s)" — a persona is a document like any other, so this
   was arguably already true and simply unstated. Made explicit rather than left as an
   inference two adapters could read differently.
3. **Testing policy gains a row for external-protocol boundaries** — transport/bridge layers
   that cannot be unit-tested without inventing a fake protocol. Bar: extract and unit-test
   the pure decision logic, then require one live smoke check per protocol operation with its
   receipt recorded as evidence. `foundryvtt-mcp`'s `sidecar/confirmation.js`,
   `actor-utils.js`, and `bridge-auth.js` are the existence proof that the pure core is
   extractable.

Kernel 0.4.0 → 0.5.0.

## Alternatives considered

- **Keep Foundry QA out of the kernel workflow entirely; live only as a pack-level
  checklist.** Costs nothing to the contract, but also produces no fallback ladder for any
  future workflow with the same shape (a live, human-attended dependency is not unique to
  Foundry). Rejected because the second-pack purpose of this work is specifically to find
  out whether the kernel generalizes — declining to generalize the one gap a second pack
  actually hit would defeat the exercise. Recorded here as the fallback if the exception in
  practice turns out to invite misuse (see Consequences).
- **Personas kernel-only, with a per-project override for each Foundry project.** Cheaper to
  reason about (one place personas live) but duplicates the persona for every Foundry project
  and gets stated nowhere either way, so it trades one ambiguity for a different maintenance
  cost. Rejected; the cascade already permits pack personas, so stating it costs one sentence.
- **A generic "unverifiable-in-CI" capability instead of `live-environment` specifically.**
  Rejected as premature generalization — one instance does not justify guessing the shape of
  the next one. Narrower now, widen later if a third project needs a materially different
  kind of unreachable dependency.

## Consequences

A workflow can now honestly declare "produces an artifact and stops" as its floor when its
subject is a live, human-operated system, instead of either lying about a file-system-only
floor or being disqualified from workflow status entirely. This is a contract change to an
`overridable: false` document, so it is treated as such: drafted with the risk stated in the
plan, not landed silently.

**The risk this decision accepts, stated plainly:** rule 1 exists to stop workflows from
being adapter features in disguise. Loosening it for one named, narrow case is defensible
because the case is real — no file-system-only floor verifies a live Foundry write — but "the
rule is inconvenient for my project" and "the rule is wrong" look identical from inside the
project that wants the exception. The wording keeps the exception scoped to
`requires: [live-environment]` specifically, and every other workflow's rule 1 obligation is
unchanged, precisely to keep this from becoming a general escape hatch. Revisit if a workflow
reaches for `live-environment` without a genuine unreachable-dependency story — that is the
signal the exception is being used as the adapter-feature loophole it was written to prevent.

One consumer (`foundryvtt-mcp`) is thin evidence for a pack, and pack-versus-project is a
boundary a single sample cannot settle. The kernel findings recorded here are what justify
`packs/foundry-vtt` existing at all; if a future review concludes these three changes should
not have generalized, the fallback is to demote them to project overrides and keep the pack
as project-layer-only content.
