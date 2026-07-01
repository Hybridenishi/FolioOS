# ADR-0006: Files first; no CLI in v0.1

- **Date:** 2026-07-01
- **Status:** accepted

## Context
A `folioos` CLI (init/update/doctor) would improve DX, but it's a solo-maintained software project inside a methodology project — a bus-factor-one dependency. Meanwhile, the lowest common denominator across every current and future AI agent is: it can read and write files.

## Decision
Every FolioOS operation is specified as a hand-operable file procedure (adoption guide, compile procedures, hash approval, staleness checks). Tooling may be added later but must implement the documented procedures exactly — the documents remain the system of record.

## Alternatives considered
- **CLI now:** front-loads maintenance before usage patterns are known; risks making the kernel unusable without it.
- **Never build tooling:** unnecessarily dogmatic; the decision is sequencing, not prohibition.

## Consequences
Some operations are slightly tedious (manual compile, manual staleness checks). Trigger to revisit: the same manual procedure performed often enough to be error-prone in practice — automate that procedure first, as a thin wrapper.
