---
id: packs/foundry-vtt/standards/deployment-integrity
type: standard
layer: pack:foundry-vtt
scope: on-plan
requires: []
overridable: true
version: 1
---

# Standard: Deployment Integrity

**Policy: a deployable artifact's build inputs are derivable from the repository alone.** If a rebuild succeeds only because of files a human left on the host during earlier manual setup, the project does not build — it merely has, once, on that host.

Foundry deployments invite this failure because they are naturally multi-part: a service, a module installed into the Foundry data directory, and a reverse-proxy route, each copied into place by a different mechanism.

## Rules

- **Every runtime file the deployment needs is tracked**, including lockfiles the container build depends on. An untracked lockfile turns a reproducible build into a snapshot of one machine.
- **Adding a file to a deployable component means updating every place that enumerates files.** Find them all before writing the file, and name them in the plan step. The failure mode is silent: a file missing from any one of them passes every local test and fails only on the host.
- **Enumeration lists are a smell — prefer a single manifest.** When the same file list appears in a build definition, a copy step, and a preflight check, they will drift. Consolidating them is the fix; keeping them in sync by discipline is not.
- **Verify the deployment from inside the deployed artifact**, not from the developer's machine. A check that passes locally and fails in the container is the check you needed.
- **Build caches lie.** A deployment procedure states how to force a clean rebuild, because "my change didn't take effect" is otherwise indistinguishable from "my change is wrong."
- **Deployment scripts never print secrets and never mutate world data.** A deploy that can corrupt a live world is a migration, and migrations are checkpoint actions.

## Why this is a pack standard rather than a project note

It generalizes past Foundry — any deploy assembled from copy steps has it — and it may belong in the kernel eventually. It sits here until a second, non-Foundry project has been bitten by it, because a standard promoted from one project's incident is a guess about generality. One more data point decides it.
