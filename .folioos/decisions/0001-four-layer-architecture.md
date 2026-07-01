# ADR-0001: Four layers, separated by rate of change

- **Date:** 2026-07-01
- **Status:** accepted

## Context
FolioOS must serve multiple projects, multiple AI agents, and multiple platform cycles for years. The main long-term risks are drift (copies diverging) and churn coupling (fast-changing parts destabilizing slow ones).

## Decision
Kernel (tool-agnostic methodology) → Packs (platform knowledge) → Adapters (per-agent compilers) → Userland (per-project state), with a one-way cascade kernel → pack → project overrides.

## Alternatives considered
- **Template copied per project:** simplest, but drifts silently and irreversibly.
- **Flat prompt library:** no override semantics, no versioning story, becomes a junk drawer.
- **Per-tool repos:** duplicates the methodology per agent; the methodology is the asset, so that's duplication of the crown jewels.

## Consequences
Methodology improvements propagate deliberately via version pins. Cost: the cascade and compile step must stay simple enough to run by hand, or the layers become bureaucracy. Revisit if a second maintainer never materializes *and* the compile step is skipped in practice.
