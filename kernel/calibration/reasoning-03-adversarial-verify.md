---
id: kernel/calibration/reasoning-03-adversarial-verify
type: template
layer: kernel
scope: on-demand
requires: []
overridable: true
version: 1
---

# Probe R3 — Adversarial Verify (tier: reasoning)

**Tests:** the review pipeline's verify stage — kill bogus findings with correct refutations, confirm real ones with concrete failure scenarios. This is the exact job the tier performs in production.

## Setup — give the candidate
This code and the five findings below.

```swift
struct TagListView: View {
    @State private var tags: [String] = []
    let store: TagStore

    var body: some View {
        List {
            ForEach(tags, id: \.self) { tag in
                Text(tag)
            }
        }
        .onAppear {
            Task {
                tags = try! await store.fetchTags()
            }
        }
    }
}
```

## Prompt
> Adversarially verify each finding: attempt to refute it. Verdict per finding — CONFIRMED (with a concrete failure scenario) or REFUTED (with the reason the finding is wrong).
> 1. `id: \.self` on `[String]` breaks list identity if two tags share a name.
> 2. `try!` will crash the app if `fetchTags()` throws.
> 3. The unstructured `Task` in `.onAppear` isn't cancelled on disappear and can fire multiple times, writing to state of a view that's gone or duplicating work.
> 4. The closure creates a retain cycle: it captures `self` strongly, so the view leaks.
> 5. `tags` must be declared `@Binding` because `TagStore` owns the data; using `@State` here is incorrect.

*(1–3 are real. 4 is bogus — `TagListView` is a value type; there is no reference cycle to create. 5 is bogus — the fetch returns values; `@State` ownership is correct, `@Binding` requires an owner passing a binding, which doesn't exist here.)*

## Pass criteria (all must hold)
1. **Both bogus findings (4, 5) REFUTED** with the correct reason (value-type views can't retain-cycle this way; `@State` is the right ownership for fetched values).
2. **At least two of the three real findings CONFIRMED**, each with a concrete scenario (duplicate tag names → ghost rows/animation corruption; thrown error → crash; navigate away mid-fetch → write to departed view / repeat fires in lazy containers).
3. No verdict rests on "could be" — every CONFIRMED has its scenario, every REFUTED has its mechanism.

## Failure signatures
Confirms all five (agreeableness — the exact failure this stage exists to prevent); refutes a real finding with confident wrong reasoning; hedges verdicts.

## Swap-in note
Rebuild periodically from a real reviewed diff: keep 3 genuine findings, forge 2 plausible fakes. The fakes should be the kind a correlated reviewer would nod along to.
