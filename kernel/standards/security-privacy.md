---
id: kernel/standards/security-privacy
type: standard
layer: kernel
scope: always
requires: []
overridable: true
version: 1
---

# Security & Privacy Standard

- **No secrets in the repo, ever.** API keys, tokens, signing material live in the keychain, environment, or an ignored local config. An agent that finds a committed secret stops and reports it immediately.
- **Data minimization.** Collect and persist only what the feature needs. New data collection of any kind is a plan-level callout, never an implementation detail.
- **User data crossing a boundary is a checkpoint action.** Any change that sends user data off-device (analytics, sync, export destinations, third-party SDKs) must be named in the approved plan.
- **Third-party dependencies are a liability with a version number.** Each addition needs plan-level justification: what it does, why not first-party, its maintenance health.
- **Logging never includes user content or identifiers** beyond what debugging strictly requires; nothing sensitive at persistent log levels.
- **Fail closed.** When an auth/permission check errors, deny.
- Platform specifics (App Privacy labels, ATT, entitlements) live in the platform pack's release checklist.
