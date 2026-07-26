---
id: packs/foundry-vtt/standards/mutation-pattern
type: standard
layer: pack:foundry-vtt
scope: always
requires: []
overridable: true
version: 1
---

# Standard: Mutation Pattern

**Every write to a Foundry world follows the same four steps.** Not "every risky write" — every write. A pattern applied selectively is a pattern nobody can rely on, and the selection is always made by whoever is in the biggest hurry.

1. **Preview — read-only.** Validate the target exists, the caller may touch it, and the inputs are semantically meaningful. Return what *would* change, plus a **scoped, single-use, short-lived confirmation token**.
2. **Apply — requires that exact token.** The token is bound to the specific operation: same actor, same document, same values. A token for a different operation is rejected as a mismatch, not as an expired token — the two are different failures and the error must say which.
3. **Execute through the game system's own API**, never raw document mutation. The system owns rules behavior; going around it desynchronizes every module that hooks the documented path.
4. **Receipt — read the changed document back.** Report before and after values from a fresh read, not from what you sent. Reporting intent as outcome is how a write that silently failed gets recorded as success.

## Rules

- **Gate at the boundary that is actually reachable**, not only in the client library. A gate in the tool layer is decoration if the HTTP or socket endpoint underneath it accepts unauthenticated calls.
- **The confirmation helper takes a caller-supplied binding object.** Adding a gated operation means a new binding shape — not a new mechanism. If someone is writing a second confirmation implementation, the first one's abstraction is wrong.
- **Distinguish absent/expired from mismatched.** "Preview the operation again" and "that token is for a different operation" send the caller to different fixes.
- **Tokens are single-use and short-lived.** Consumed on success *and* on binding mismatch — a token that survives a failed attempt is a replay waiting to happen.
- **Legacy raw writes are quarantined, not imitated.** A pre-pattern mutation that still exists is technical debt with a plan attached; it is never the template for the next one.

## Why this is a standard and not a suggestion

The pattern is doing three jobs at once, and each one is why a shortcut version fails:

- **The preview is the permission check.** It is the only place where "does this make sense" is asked while nothing has happened yet.
- **The token is the binding between intent and effect.** Without it, an apply call is a request to mutate arbitrary state that happens to have been preceded by a validation of something else.
- **The receipt is the only evidence that the write landed.** In a live world, other actors — human and automated — are writing concurrently. What you sent is a hypothesis; what you read back is the fact.

Same decomposition as the [work-state contract](../../../kernel/contract/work-state.md)'s approval mechanism, arrived at independently: the machine issues the token, the caller echoes it, the binding is checked at consume time, and the outcome is verified by re-reading rather than assumed.
