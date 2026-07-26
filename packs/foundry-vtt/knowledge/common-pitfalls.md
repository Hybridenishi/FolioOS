---
id: packs/foundry-vtt/knowledge/common-pitfalls
type: knowledge
layer: pack:foundry-vtt
scope: on-demand
requires: []
overridable: true
version: 1
---

# Knowledge: Common Pitfalls

Each entry cost a live debugging session. Several contradict what the API's shape suggests, which is why they are written down rather than inferred. Verified against Foundry **v14** — delete on upgrade rather than annotating (see [version-targeting](../standards/version-targeting.md)).

## Authentication

- **Session credentials go in HTTP headers, not URL query parameters.** A Socket.IO client must send the session cookie via `extraHeaders`; passing it as a `query` parameter fails on v14. Widely-published integration examples get this wrong, so a working example found online is not evidence.
- **The handshake is four steps and the order matters:** obtain a session cookie → connect the socket with that cookie and resolve the user id from the join data → authenticate over HTTP with the id and password → reconnect so the socket carries an authenticated session. Skipping the reconnect leaves a socket that is connected but not authorized, which fails later and confusingly.

## The document-mutation protocol

Payload shapes were reverse-engineered and are unforgiving:

- **Delete takes an object with an id array** — `{ ids: [...] }`. A bare array fails. An underscore-prefixed key fails. The error does not say which.
- **Chat message `type` is an integer**, not a string, and the author field must be the id of the user the connection authenticated as. A mismatched author is rejected as a permission error, which sends you to the wrong problem.
- **Prefer the system's own API over this protocol entirely.** These shapes are documented here because legacy code uses them, not because new code should — see [mutation-pattern](../standards/mutation-pattern.md).

## Data-model traps in system documents

- **Formula fields are evaluated as dice expressions.** Putting descriptive text where a formula is expected throws an unresolved-term error at evaluation time, not at write time. Leave the formula empty when a flat value is intended.
- **Partial nested structures confuse sheets.** Writing one field of a computed structure (a skill's value without its ability, total, and passive siblings) produces a document that saves cleanly and renders wrong. Either write the complete structure or create the document without it and let the system populate it.
- **Computed fields are computed — don't send them.** Ability scores take a value; modifiers and saves derive from it. Sending a derived field either is ignored or, worse, sticks and then disagrees with its own inputs.
- **Minimal-creation payloads are a real category.** Know the smallest document that renders correctly for each type you create, and start there. Building up from minimal is debuggable; trimming down from a full document is not.

## The browser-side bridge

- **Runtime-derived values are not on the wire.** Prepared and computed data exists in a client that has run the system's derivation. A headless socket connection sees stored data only, and no amount of reading harder will produce the prepared values.
- **A bridge in a browser client is a session, and sessions end.** Pair only after validating the browser's authenticated session server-side, hold a short-lived in-memory token, and expire it on inactivity. Never ship a credential in module source — the module is served to every client.
- **When the bridge is unavailable, error explicitly.** Falling back to stored values in place of prepared ones returns plausible wrong numbers, which is worse than returning nothing.

## Operational

- **Reloading a module means a hard refresh in an authenticated client**, and file-copy alone does not do it. A deploy that appears to have no effect has usually not been loaded.
- **Container build caches will serve you the previous version of your own code.** Know the clean-rebuild incantation before you need it.
