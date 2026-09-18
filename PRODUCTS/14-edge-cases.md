# Section 14 · Functional edge cases — verify the contract at the boundary



Tests that don't exercise the boundaries of a function's contract aren't really testing the function — they're just running it once and hoping nothing crashes. The boundary is where the function's *behavior* is most informative: it's the line between "the function handles X correctly" and "the function does Y for a different X." Every public function in the five services has a contract. Every contract has a boundary. Every boundary needs a **functional edge-case test**.

### The rule

Every public function ships with an edge-case test file at `qa/edges/<concept>_edges.rs`. Each file is a list of behaviors verified *at the boundary*, with each test citing the contract line it is checking. A test that only asserts "did not panic" at the boundary fails review.

### What is a "functional edge case"?

A functional edge case is a test that:

1. Targets a **boundary** of the function's input or output space — quantitative boundaries (zero, one, max, max+1), type boundaries (empty string, single char, off-by-one), encoding boundaries (pure ASCII, pure UTF-8, surrogate pairs), order boundaries (empty list, single element, all-equal), or threshold boundaries (exactly at, just below, just above).
2. **Asserts the function's documented behavior at that boundary.** Not "no panic." "What it did."
3. **Cites the contract clause being tested.** A test without a comment pointing to the contract is a regression waiting to happen.
4. Reveals the boundary **by being wrong by definition if the contract drifts.** If the test still passes when the contract is violated, the test is decoration.

### Categories of functional edge cases, per public function

Each function gets all five categories covered. If a category is irrelevant (e.g., numeric precision for a string-only function), document why and skip it — but the skip itself is a contract statement.

| Category | What it tests | Concrete questions |
|---|---|---|
| **Quantitative boundaries** | Numeric limits of input or output | What at zero? At one? At `usize::MAX`? One past the cap? |
| **Type boundaries** | String/structural limits | Empty string? Single character? Single-byte? At-length? One past length? |
| **Encoding boundaries** | Unicode and bytes | Pure ASCII? Pure UTF-8? Surrogate pairs without pairs? Mixed BIDI marks? NUL bytes? |
| **Order/tie boundaries** | Sorted and grouped outputs | Empty list? One element? All-equal? Equal scores? Tie-break rules? |
| **Threshold/inequality boundaries** | Strict vs. non-strict comparisons | Score == threshold? Score > threshold? Score < threshold? NaN? Infinity? |

### Directory layout

`qa/` adds an `edges/` directory alongside `functional/`. Each source concept gets one file in each:

```
service/qa/
├── README.md
├── functional/
│   ├── parse_manifest.rs              # happy path + invariants
│   ├── probe_lookup.rs
│   └── ...
├── edges/                             # boundary behaviors of the same functions
│   ├── parse_manifest_edges.rs
│   ├── probe_lookup_edges.rs
│   └── ...
├── invariants/
│   ├── determinism.rs
│   └── ...
├── adversarial/
│   └── ...
├── cli/
│   └── ...
└── fuzzer/
    └── ...
```

Each `<concept>_edges.rs` file is structured: a header comment listing the contract clauses being verified, then one test per clause. ≤ 256 LoC per file (per §2).

### One worked example: edge cases for a scoring function

A contrived but realistic section. The contract for `score::compute(manifest, threshold) -> ScoreReport`:

```
contract score::compute:
    returns a ScoreReport with one entry per dep
    each entry's score is in [0.0, 100.0]
    an entry is "flagged" iff score > threshold           # STRICT inequality
    no entry is silently dropped, even if scoring failed
    on a scoring failure (division by zero, NaN input, ...) return Err(ScoreError)
```

```rust
//! Edge cases for `score::compute`.
//!
//! Contract clauses exercised here:
//!   C1: each entry's score in [0.0, 100.0]
//!   C2: "flagged" iff score > threshold (strict)
//!   C3: no silent dropping on scoring failure
//!   C4: NaN / Infinity inputs are typed errors, not panics, not silent zeros

use score::{compute, ScoreReport, ScoreError};

fn fixture_manifest_with_score(score: f32) -> Manifest { /* ... */ }
fn policy_threshold(t: f32) -> Policy { /* ... */ }

#[test]
fn edge_score_exactly_equals_threshold_is_not_flagged_c2() {
    // Contract: "flagged iff score > threshold." Equal is *not* flagged.
    let m = fixture_manifest_with_score(80.0);
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries[0].flagged, false);
}

#[test]
fn edge_score_just_above_threshold_is_flagged_c2() {
    let m = fixture_manifest_with_score(80.0001);
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries[0].flagged, true);
}

#[test]
fn edge_score_just_below_threshold_is_not_flagged_c2() {
    let m = fixture_manifest_with_score(79.9999);
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries[0].flagged, false);
}

#[test]
fn edge_score_at_zero_lower_bound_c1() {
    let m = fixture_manifest_with_score(0.0);
    let r = compute(&m, &policy_threshold(50.0)).unwrap();
    assert!(r.entries[0].value >= 0.0);
}

#[test]
fn edge_score_at_hundred_upper_bound_c1() {
    let m = fixture_manifest_with_score(100.0);
    let r = compute(&m, &policy_threshold(50.0)).unwrap();
    assert!(r.entries[0].value <= 100.0);
}

#[test]
fn edge_oversized_score_is_clamped_not_promoted_c1() {
    // If 105.0 comes in via some upstream overflow, contract says
    // clamp to 100.0 — not silently pass the threshold as 105.
    let m = fixture_manifest_with_score(105.0);
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries[0].value, 100.0);  // clamped, not raw
}

#[test]
fn edge_nan_score_is_typed_error_not_panic_c4() {
    let m = fixture_manifest_with_score(f32::NAN);
    let r = compute(&m, &policy_threshold(80.0));
    assert!(matches!(r, Err(ScoreError::NonFinite)));
}

#[test]
fn edge_infinity_score_is_typed_error_not_panic_c4() {
    let m = fixture_manifest_with_score(f32::INFINITY);
    let r = compute(&m, &policy_threshold(80.0));
    assert!(matches!(r, Err(ScoreError::NonFinite)));
}

#[test]
fn edge_zero_dependencies_returns_empty_report_c5() {
    let m = Manifest::empty();
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries.len(), 0);
}

#[test]
fn edge_one_dependency_does_not_panic_on_tie_break_c6() {
    let m = Manifest::with_deps_n_scored(1, 80.0);
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries.len(), 1);
    assert_eq!(r.entries[0].flagged, false);  // equal-to-threshold, not flagged
}

#[test]
fn edge_max_dependencies_finishes_in_bounded_memory_c7() {
    // 100k deps must not OOM. The binary's resource envelope is part of the contract.
    let m = Manifest::with_deps_n_scored(100_000, 0.0);
    let r = compute(&m, &policy_threshold(80.0)).unwrap();
    assert_eq!(r.entries.len(), 100_000);
}
```

Every test cites a contract clause (the `c<N>` suffix in the name). The diff-to-contract linkage is the test's story.

### What goes in each test

The five-part shape that any QA review can audit:

1. **What boundary is being tested?** (in the test name)
2. **What does the contract say should happen?** (in the leading comment)
3. **What input we use.** (in the fixture call)
4. **What output we expect.** (in the assertion)
5. **What the opposite looks like, so a reviewer can spot a regression.** (in a closing comment if non-obvious)

A test missing any of these is fixable in review.

### Anti-patterns to reject in code review

```rust
// REJECT: tests nothing at the boundary
#[test]
fn edge_does_not_crash() {
    let _ = score::compute(&anything(), &anything());
}

// REJECT: tests "is_ok" instead of behavior
#[test]
fn edge_score_at_threshold_is_ok() {
    assert!(score::compute(&m(), &threshold(80.0)).is_ok());
}

// REJECT: tests the wrong boundary
#[test]
fn edge_score_at_fifty_is_flagged() {  // 50 isn't the threshold we said
    assert_eq!(compute(&m, &threshold(80.0)).unwrap().entries[0].flagged, true);
}

// REJECT: a test that passes when the contract is violated
#[test]
fn edge_score_infinity_is_handled() {
    let r = score::compute(&m(), &threshold(80.0));
    if r.is_ok() { assert_eq!(r.unwrap().entries[0].value, 0.0); }  // silent zero
    // This silently turning Infinity into 0.0 is exactly the contract violation
    // we wanted to catch — a "passing" test that hides the bug.
}
```

Reject all four. The reviewer who wrote any of them needs to re-read the contract.

### How this connects to §13, §9, and §4

- **§13** says *every function* gets functional QA. **§14** says *every function's QA* must include edge cases. §13 + §14 are the contract-enforcement layer.
- **§9** says the openOADA substrate is the engine. §14 ensures the engine doesn't lose correctness at boundaries — which is exactly where adversarial bugs in capability-based systems have surfaced before.
- **§4** says every service starts from one question. §14 says the *answer* to that question is verified at every boundary the answer touches. If you can't write §14 tests for your service, you don't yet know what your service does.

### One-line policy

> A "no panic" test is not a test. A test is a behavioral assertion at a boundary, citing the contract clause it is verifying.
