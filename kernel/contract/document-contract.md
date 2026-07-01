---
id: kernel/contract/document-contract
type: contract
layer: kernel
scope: on-demand
requires: []
overridable: false
version: 1
---

# Document Contract

Every kernel and pack document conforms to this schema. Adapters compile *mechanically* against it — no adapter may interpret prose to decide what a document is, when it loads, or how it merges. The schema is deliberately tiny: you can grow a schema, you can't shrink one. Bodies stay human-readable markdown; the contract is metadata only.

## Frontmatter schema (v1)

```yaml
---
id: kernel/standards/testing-policy   # repo-relative path, no extension. Unique.
type: standard        # contract | principle | persona | standard | workflow
                      # | template | checklist | knowledge | override
layer: kernel         # kernel | pack:<name> | project
scope: always         # always | on-plan | on-review | on-release | on-demand
requires: []          # capability ids this document depends on (see capability-model)
overridable: true     # contracts and the autonomy model are false
version: 1            # per-document integer; bump when meaning changes, not wording
---
```

### `scope` — when a document must be in an agent's context

| scope | Meaning | Compile target (typical) |
|---|---|---|
| `always` | Needed on every task | Distilled into the always-loaded instruction file, within budget |
| `on-plan` | Needed while writing/approving a plan | Planning skill/command |
| `on-review` | Needed during review pipeline | Review skills / reviewer agents |
| `on-release` | Needed during release workflow | Release skill |
| `on-demand` | Reference material, loaded when relevant | Standalone skill or plain file lookup |

`scope` exists for context economy: instruction adherence degrades as always-loaded context grows, so `always` is a scarce resource. Adapters enforce a hard budget on the compiled always-layer (see each adapter's compile procedure); exceeding it is a build failure, resolved by demoting documents to narrower scopes or distilling harder.

## Override documents

Project (and pack) overrides are documents of `type: override` with three extra fields:

```yaml
---
id: project/overrides/testing-policy
type: override
layer: project
target: kernel/standards/testing-policy   # id of the document being overridden
operation: extend                          # extend | replace | disable
rationale: "This app ships a medical-adjacent feature; coverage bar is higher."
scope: always        # usually inherits the target's scope; may differ
requires: []
overridable: true
version: 1
---
```

- **`extend`** — body is appended to the target's body as an "In this project, additionally:" section.
- **`replace`** — body substitutes the target's body entirely. Use sparingly; you lose upstream improvements silently.
- **`disable`** — target is excluded from compilation. Body must explain why.
- **`rationale` is mandatory.** An override without a why is a fork with better manners.

A target with `overridable: false` rejects all overrides; the compile procedure must fail loudly, not skip silently.

## Cascade merge semantics

Adapters implement exactly this algorithm — it is specified once, here, so every adapter merges identically:

1. Collect all kernel documents, indexed by `id`.
2. Apply pack overrides (documents in the active pack with `type: override`) to their targets, in pack file order.
3. Add all non-override pack documents (new `id`s).
4. Apply project overrides (from `.folioos/overrides/`) to the result.
5. Compile the merged set by `scope`, honoring `requires` against the adapter's capability manifest: a document requiring an unavailable capability compiles to its declared fallback (see capability-model) or is omitted with a logged warning — never silently half-included.

Conflict rule: overrides apply in layer order (pack, then project); within a layer, two overrides targeting the same `id` are a validation error, not a merge.

## Validation checklist (run by hand or by future tooling)

- [ ] Every document has frontmatter with all required fields
- [ ] Every `id` matches its file path and is unique
- [ ] Every `override` names a `target` that exists and is `overridable: true`
- [ ] Every `rationale` on an override is non-empty
- [ ] Every capability in `requires` exists in the capability vocabulary
- [ ] No two overrides in the same layer share a `target`
