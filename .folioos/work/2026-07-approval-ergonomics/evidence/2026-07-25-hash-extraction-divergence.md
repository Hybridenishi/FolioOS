# Evidence: the plan-body extraction is underspecified, and two readings disagree

- **Date:** 2026-07-25
- **Subject:** `kernel/contract/work-state.md` §"Computing the hash"
- **Type:** reproduction

## What the contract says

> `shasum -a 256` over the plan file content below the frontmatter's closing `---` (exclusive), byte-exact.

"Below the closing `---`, exclusive" does not say whether the blank line that
conventionally follows the closing delimiter is part of the body. Both readings are
faithful to the sentence. They produce different hashes.

## Reproduction

Target: `kernel/templates/plan.md` at commit `1c9ef36`.

```sh
# Reading B — everything after the closing delimiter, blank line included
awk 'n>=2{print} /^---$/{n++}' kernel/templates/plan.md | shasum -a 256
# 7ced3e1b7c46a64a466e857549760ff6c66daa58c347ec59fb0cce5fbbef7389

# Reading A — same, with the leading blank line treated as part of the delimiter
awk 'n>=2{print} /^---$/{n++}' kernel/templates/plan.md | sed '1{/^$/d}' | shasum -a 256
# b56fcb9b10cbf10c21ac1b8fc4b8756e43988d4540cc63bf2c78ac88f3e637d6
```

No shared prefix. Truncation does not rescue it: the first 8 characters are
`7ced3e1b` and `b56fcb9b`.

`shasum -a 256` and `sha256sum` agree with each other on identical input, so this is
not a tool difference — it is an input-boundary difference.

## Why it matters

Rule 2 of the work-state contract makes hash mismatch mean *the plan changed after
approval → stop and re-gate*. Under two defensible extractions, a plan approved by a
human using one reading fails verification for an agent using the other. The contract's
central promise — "any agent, in any session, answers 'am I authorized?' from a
file" — does not hold across agents that read this sentence differently.

The failure is fail-closed (it blocks execution rather than authorizing it), so it
degrades trust rather than safety. But its symptom is indistinguishable from real
drift: an agent reports "the plan changed after approval" about a plan nobody touched.
The likely human response to a spurious mismatch is to stop verifying hashes.

## Additional finding: `---` inside the body is safe, but only for some extractions

Plan bodies routinely contain `---` lines — the plan template itself embeds a
frontmatter example inside a fenced code block. The `awk 'n>=2{print} /^---$/{n++}'`
form handles this correctly, because it prints every line once two delimiters have been
seen and simply keeps counting. A range-based reading (`sed -n '/^---$/,/^---$/!p'`)
truncates the body at the first embedded `---`. Both are plausible implementations of
the contract's prose sentence; only one is correct.

## Bearing on the plan

This is why the plan in this directory pins one normative command rather than
describing the boundary in prose. Shortening the human's transcription burden is the
requested change; specifying the extraction is the change that makes any hash rule —
old length or new — actually portable.
