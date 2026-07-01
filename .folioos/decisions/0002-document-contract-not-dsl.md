# ADR-0002: Document contract as tiny frontmatter schema, not a DSL

- **Date:** 2026-07-01
- **Status:** accepted

## Context
Adapters must compile documents mechanically; free-form markdown forces each adapter to interpret prose, and interpretations diverge. But over-formalizing turns methodology writing into programming.

## Decision
Every kernel/pack document carries a seven-field frontmatter block (`id, type, layer, scope, requires, overridable, version`); bodies stay human markdown. Override semantics are three explicit operations with mandatory rationale. Cascade merge is specified once in the kernel.

## Alternatives considered
- **No schema:** each adapter interprets prose; divergence is guaranteed and undetectable.
- **Structured format (YAML/JSON documents, custom DSL):** machine-friendly, human-hostile; methodology authorship must stay as easy as writing markdown or it stops happening.

## Consequences
Compilation is mechanical; validation is a checklist. Constraint accepted: you can grow a schema but not shrink one, so fields are added only under demonstrated need (kernel major bump).
