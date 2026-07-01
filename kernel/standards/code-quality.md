---
id: kernel/standards/code-quality
type: standard
layer: kernel
scope: always
requires: []
overridable: true
version: 1
---

# Code Quality Standard

Platform-agnostic rules. Language specifics live in packs.

- **Match the surrounding code.** New code reads like the code around it — naming, idiom, comment density. Consistency beats personal preference.
- **Small, coherent diffs.** One unit of work per change. Refactors that "came along for the ride" go in their own change (or a candidate note).
- **Comments state constraints, not narration.** Write a comment only for what the code can't say: a non-obvious invariant, a workaround with its trigger, a deliberate deviation. Never "what the next line does" or "why my change is correct."
- **No dead code.** Unused paths are deleted, not commented out — git remembers.
- **Errors are handled or propagated, never swallowed.** An empty catch needs a comment defending itself.
- **Names tell the truth.** A function named `save` that also syncs is a bug in the name.
- **No speculative generality.** Build for the current requirement; extension points need a concrete second use case (or an ADR).
- **Every bug fix ships with a test that fails without it.** (See [testing-policy](testing-policy.md).)
