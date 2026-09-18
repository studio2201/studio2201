# Section 18 · Performance budgets — one number per verb, measured with `std::time`, no crates


**A regression caught is cheaper than a regression shipped.** Every public verb in every service has a budget: a wall-clock number (median of 5 runs, ±25%) that the binary is allowed to spend on the canonical input. The budget is documented in `tools/perf/budget.md` and the measurement is a `#[test]` in the service's existing test target, hand-rolled with `std::time::Instant`. No `criterion`, no `bencher`, no `hdrhistogram` — `std::time` is enough to catch a regression that doubles the latency of a verb, which is what we actually need to catch.

### Rule

Each service ships:

1. `tools/perf/budget.md` — a table of one row per public verb, listing the verb, the canonical input, the median budget, and the tolerance.
2. A `#[test]` in `tests/integration.rs` (the existing test target) named `perf_<verb>_within_budget` that:
   - Builds an in-memory fixture appropriate to the service.
   - Runs the verb in a warm-up pass (not measured).
   - Runs the verb 5 times. Records the median.
   - Prints `verb=<name> median_ms=<value> budget_ms=<value>` to stdout.
   - Asserts the median is within `budget * (1 + tolerance)`. Exits non-zero on regression.

The bench is a `#[test]`, not a `[[bin]]`, because §12 says no Cargo.toml cruft. The existing test target is the honest place for it.

### Initial budget defaults

These are honest placeholders — first cuts, not aspirational. They will be tuned once binaries exist.

| Service | Verb | Canonical input | Budget (median) | Tolerance |
|---|---|---|---|---|
| **Vigil** | `vigil scan` | 10,000 deps in-memory (synthetic `package.json`) | 800 ms | ±25% |
| **Proven** | `proven sign` | 1 MB artifact (synthetic binary blob) | 1.2 s | ±25% |
| **Aegis** | `aegis scan` | 10,000 LoC source file (synthetic) | 600 ms | ±25% |
| **Snip** | `snip check` | 200 KB diff (synthetic LLM diff) | 400 ms | ±25% |
| **Boneyard** | `boneyard enrich` | 1,000-repo hall (synthetic org metadata) | 1.5 s | ±25% |

The median-of-5 protocol damps noise without requiring statistical machinery. If a future verb needs more rigor, add it; don't replace the protocol.

### Why no `criterion`

§12 forbids Cargo dependencies. `criterion` is the canonical Rust bench crate, but pulling it in would mean: a `dev-dependencies` entry in `Cargo.toml`, a separate `--bench` harness, and a richer output format (PDFs, comparison plots). We don't need any of that. The bench is a regression gate, not a publication-quality measurement. `std::time::Instant` paired with `std::process::exit(1)` is the entire machinery.

### Contract clauses

| Clause | What it requires |
|---|---|
| **C1** | `tools/perf/budget.md` has **one row per public verb**. A verb without a budget row fails the clause. |
| **C2** | The bench measures with `std::time` only. No `criterion`, no `bencher`, no `pprof`. A `grep -L "std::time"` on `bench.rs` should return zero matches (i.e., every bench uses `std::time`). |
| **C3** | The unit of measurement is **median-of-5**, not mean-of-N, not min, not max. Mean is sensitive to outliers; median damps jitter. |
| **C4** | A regression is a **CI gate**, not a one-shot run. The bench is a `#[test]`. `cargo test --release` runs it on every commit. Exceeding the budget fails the test target. |
| **C5** | The bench fixture is **synthetic and committed to the test target** (not fetched at test time). Network-free regression measurement. Consistent with §12's no-deps stance. |
| **C6** | The bench's printed output is **line-oriented and grep-friendly**: `verb=<name> median_ms=<float> budget_ms=<float> pass=<bool>`. CI log scrapers can pick the lines up without parsing Rust's test runner output. |

### Worked example — Vigil's bench (sketch)

A simplified version of the `perf_vigil_scan_within_budget` test, inlined into `tests/integration.rs`:

```rust
//! tools/perf/bench.rs — invoked via `cargo test --release perf_vigil`.
//! Measures the canonical `vigil scan` verb on a 10k-dep synthetic fixture.
//! Asserts median ≤ 800ms * 1.25 = 1000ms (budget + tolerance).
//! Honors §18: std::time only, median-of-5, line-oriented output.

use std::time::Instant;

#[test]
fn perf_vigil_scan_within_budget() {
    let fixture = synth_manifest_with_n_deps(10_000);   // 10k deps
    let budget_ms: f64 = 800.0;
    let tolerance: f64 = 0.25;
    let ceiling_ms = budget_ms * (1.0 + tolerance);

    // Warm-up: load fixture, prime allocator, but don't measure.
    let _ = vigil::scan(&fixture, &policy());

    // Five timed runs.
    let mut samples = Vec::with_capacity(5);
    for _ in 0..5 {
        let t = Instant::now();
        let _ = vigil::scan(&fixture, &policy());
        let elapsed_ms = t.elapsed().as_secs_f64() * 1000.0;
        samples.push(elapsed_ms);
    }
    samples.sort_by(|a, b| a.partial_cmp(b).unwrap());
    let median_ms = samples[2];   // median of 5

    let pass = median_ms <= ceiling_ms;
    println!(
        "verb=vigil_scan median_ms={:.3} budget_ms={:.0} pass={}",
        median_ms, budget_ms, pass
    );
    assert!(pass, "vigil_scan regression: median {:.1}ms > ceiling {:.1}ms",
            median_ms, ceiling_ms);
}
```

The same skeleton ships five times — once per service, parameterized by verb and budget.

### Why median-of-5 (not mean-of-N or min)

| Statistic | Behavior |
|---|---|
| **Min** | Hides regressions — if four of five runs are slow and one is fast, the min is still fast. |
| **Mean** | Sensitive to a single outlier (e.g., a GC pause on run 3 of 5 inflates the mean). |
| **Median** | One outlier does not move it. Five samples are enough that the median is stable across reruns. |
| **Max** | A single noisy run fails the gate, even if the binary is healthy. |

Median-of-5 is the cheapest protocol that gives a stable, regression-sensitive measurement.

### What `tools/perf/budget.md` looks like

```markdown
# Performance budget — vigil

| Verb | Canonical input | Budget (median) | Tolerance |
|------|------------------|------------------|-----------|
| `vigil scan` | 10,000 deps (synthetic `package.json`) | 800 ms | ±25% |

## How to run the bench

```bash
cargo test --release perf_vigil_scan_within_budget -- --nocapture
```

## When to update the budget

Update the budget only after a documented change to the verb's algorithm
or to the canonical input. A budget change without an algorithm change
is a regression hiding in plain sight — review will reject it.

## What the bench does NOT measure

- Cold-start latency (process spawn, dynamic linking). Out of scope.
- Network latency to a registry. Out of scope (the bench is offline).
- Memory ceiling. §14 covers resource-envelope tests separately.
```

The five per-service budgets follow the same template; only the verb name, the fixture description, and the budget number change.

### Connection to §3 (Zero Trust), §12 (From Scratch), §13 (QA), §14 (Edge Cases)

- **§3 (zero trust).** §3 says "verify everything." §18 says "and the verification has a numeric threshold." A `cargo test --release` that passes is not just "it didn't crash"; it's "it didn't regress beyond the budget."
- **§12 (zero deps).** §18 is the most concrete expression of §12 in the framework: `std::time::Instant` is enough. No `criterion`. No `bencher`. The bench proves §12's premise — that std-only code can ship with rigorous measurement.
- **§13 (functional QA).** §13 says "every function has a test." §18 says "every public verb has a numeric test." The bench is a `#[test]` — it lives in the same test target as §13's functional tests.
- **§14 (edge cases).** The canonical input is itself a boundary: 10k deps, 1 MB artifact, 200 KB diff, 1k-repo hall. Each fixture is at the boundary of what the verb is documented to handle. §14 covers the inputs; §18 covers the latency.

### How to audit §18

```bash
# A18-A: every bench.rs uses std::time (no criterion)
for s in vigil proven aegis snip boneyard; do
  grep -L "std::time" studio2201/$s/tools/perf/bench.rs \
    || echo "NO std::time: studio2201/$s/tools/perf/bench.rs"
done

# A18-B: every bench.rs uses median-of-5 (no mean-of-N)
for s in vigil proven aegis snip boneyard; do
  grep -q "median" studio2201/$s/tools/perf/bench.rs \
    || echo "NO median: studio2201/$s/tools/perf/bench.rs"
done

# A18-C: every budget.md has at least one verb row
for s in vigil proven aegis snip boneyard; do
  awk '/^\| `/' studio2201/$s/tools/perf/budget.md | grep -q '.' \
    || echo "NO VERB ROWS: studio2201/$s/tools/perf/budget.md"
done
```

A failure in any of the three lines is a §18 contract violation.

### One-line policy

> A bench without a budget number is a stopwatch; a bench with a budget number is a regression gate.
