# ADR-0003: Abstract capabilities with declared fallback ladders

- **Date:** 2026-07-01
- **Status:** accepted

## Context
Agents differ in what they can do (subagents, running apps, MCP), not just what format they read. Workflows written against one agent's abilities are unportable in practice even when the files are "tool-agnostic."

## Decision
A kernel capability vocabulary; per-adapter manifests; every workflow declares requirements and a fallback ladder whose floor requires only `file-system`. Degradation is authored, not improvised at run time.

## Alternatives considered
- **Lowest-common-denominator workflows:** wastes capable agents; the review pipeline's parallel form is genuinely better when available.
- **Per-agent workflow forks:** the drift problem again, one level down.

## Consequences
Workflows cost an extra section to write, and "capable" agents must still honor the ladder they're on. Gained: the multi-agent claim is mechanical truth, and MCP integrations fold in as capabilities instead of a parallel concept.
