---
id: kernel/principles/model-routing
type: principle
layer: kernel
scope: on-plan
requires: []
overridable: true
version: 1
---

# Model Routing

Different work deserves different model tiers. Routing is policy, set here once, so it's consistent and tunable — not a per-session habit. Plans carry a suggested tier and effort per step (see [templates/plan.md](../templates/plan.md)).

## Tiers (abstract — survive provider changes)

| Tier | Use for | Claude mapping (2026) |
|---|---|---|
| **reasoning** | Planning, architecture, tradeoff analysis, review synthesis, debugging gnarly issues | Opus-class (or above) |
| **implementation** | Writing code against an approved plan, test authoring, refactors with clear intent | Sonnet-class |
| **utility** | Doc regeneration, changelog entries, mechanical renames, formatting, summaries | Haiku-class, given precise starting instructions |

Other vendors map by capability equivalence, recorded in that vendor's adapter.

## Routing rules

1. **Plans are written at reasoning tier.** The plan is where the thinking happens; economizing there is false economy.
2. **Execution defaults to implementation tier**, stepping up to reasoning tier for any step the plan flagged as high-risk or ambiguous.
3. **Utility-tier work needs a precise brief.** Weaker tiers execute instructions; they don't recover from vague ones. The plan (or the doc template) is the brief.
4. **Review pipeline:** persona reviews at implementation tier; the adversarial verify pass and the synthesis at reasoning tier — verification is where correlated errors get caught.
5. When a step misbehaves at its assigned tier twice, escalate one tier rather than retrying a third time.
