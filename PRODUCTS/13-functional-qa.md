# Section 13 · Functional QA — `qa/` per repo, function-by-function



A passing `cargo test` is *not* evidence that a function does what its signature says. It only proves the binary didn't panic and returned *something*. That something might be wrong, off-by-one, malformed under stress, or silently lossy. **The five services need QA that reads the function's *contract* and tests against that contract directly.**

### The rule

Every public function in the five services ships with a `qa/functional/<concept>.rs` test file. The file's first 5–10 lines are a prose description of what the function is supposed to do. The tests after that prose exercise the *contract*, not the implementation:

- **Valid inputs.** Does it produce the documented output for the documented input shape?
- **Boundary inputs.** Empty, minimal, maximal. Off-by-one cases. The shape that's one chunk past the cap.
- **Malformed inputs.** Garbage in → typed error out (not panic, not "best-effort guess").
- **Invariants.** Whatever the contract promises to maintain — output ranges, monotonicity, idempotency, idempotence-after-retries — is checked.
- **Determinism.** Same input twice → byte-identical output, modulo documented non-determinism (timestamps in audit rows, etc.).
- **Round-trip.** If the function writes a thing, can you re-read it? If it's a lossless transformation, prove that.

A test that only asserts `assert!(result.is_ok())` is not enough. Reject that test. The whole QA gate fails on it.

### Directory layout

Every service repo carries a `qa/` directory that mirrors the source tree's `src/`:

```
service/qa/
├── README.md                          # QA philosophy + how to run
├── functional/
│   ├── parse_manifest.rs              # ← mirrors src/manifest.rs's public surface
│   ├── probe_lookup.rs                # ← mirrors src/probe.rs
│   ├── score_compose.rs               # ← mirrors src/score.rs
│   ├── render_report.rs               # ← mirrors src/report.rs
│   └── policy_check.rs                # ← mirrors src/policy.rs
├── invariants/
│   ├── determinism.rs                 # double-run = byte-identical
│   ├── empty_input.rs                 # empty + edge-case invariants
│   └── idempotence.rs                 # report-after-fix + re-scan
├── adversarial/
│   ├── malformed_manifest.rs          # garbage JSON, path-traversal names, BOM, nesting bomb
│   ├── whitespace.rs                  # tabs, CR-only, empty bytes
│   └── unicode.rs                     # NFC/NFD, RTL, zero-width chars
├── cli/
│   ├── smoke.sh                       # binary runs, --help well-formed, exit codes correct
│   ├── subcommands_present.sh         # all verbs from §7 are listed
│   └── fixtures/                      # black-box sample projects (npm, cargo, mixed)
└── fuzzer/
    ├── README.md                      # how to run the long-form fuzz
    ├── parse_manifest_fuzz.rs         # mutator + assertions on parser
    └── score_roundtrip_fuzz.rs        # generate → score → emit → re-parse
```

### What goes in each `qa/functional/<concept>.rs` file

A worked example for one Vigil function. The pattern is identical for every concept in every service.

```rust
//! Functional QA for `manifest::parse`.
//!
//! `parse_manifest` is supposed to:
//!   1. Detect manifest flavor (npm / cargo / pyproject / etc.) by file name + content sniff.
//!   2. Extract a list of dependencies, each with a name and a version constraint.
//!   3. Reject malformed input with `AppError::Parse` or `AppError::Invalid`, never panic.
//!   4. Be deterministic: byte-identical input → byte-identical output.
//!   5. Refuse path traversal in any name field (zero-trust boundary, §3).
//!
//! These tests are not "does it not crash." They are "does it keep its contract."

use std::path::Path;

#[test]
fn parse_manifest_npm_flavor_extracts_dependencies() {
    let m = manifest::parse(Path::new("qa/cli/fixtures/sample/package.json")).unwrap();
    assert_eq!(m.flavor, Flavor::Npm);
    assert!(!m.deps.is_empty());
    for d in &m.deps {
        assert!(!d.name.is_empty());
        assert!(!d.version.is_empty());
    }
}

// ... similar for cargo.lock and pyproject.toml ... //

#[test]
fn parse_manifest_malformed_returns_typed_error_not_panic() {
    let r = manifest::parse_string("this is not json");
    assert!(matches!(r, Err(AppError::Parse(_))));
    let r = manifest::parse_string("");
    assert!(matches!(r, Err(AppError::Parse(_))));
}

#[test]
fn parse_manifest_path_traversal_in_name_field_is_rejected() {
    let bad = r#"{"dependencies": {"../../etc/passwd": "*"}}"#;
    assert!(matches!(
        manifest::parse_string(bad),
        Err(AppError::Invalid(_))
    ));
    let bad = r#"{"dependencies": {"node_modules/.bin/ls": "*"}}"#;
    assert!(matches!(
        manifest::parse_string(bad),
        Err(AppError::Invalid(_))
    ));
}

#[test]
fn parse_manifest_is_byte_deterministic() {
    let fixture = "qa/cli/fixtures/sample/package.json";
    let a = manifest::parse(Path::new(fixture)).unwrap();
    let b = manifest::parse(Path::new(fixture)).unwrap();
    assert_eq!(a, b);  // requires impl PartialEq on Manifest
}

#[test]
fn parse_manifest_round_trips_through_serialize() {
    let original = manifest::parse_string(r#"{"name":"x","dependencies":{"a":"1"}}"#).unwrap();
    let written = original.serialize_to_string();
    let reread = manifest::parse_string(&written).unwrap();
    assert_eq!(original, reread);
}
```

### What's in `qa/invariants/`

System-level properties that don't fit a single function. These are short — sometimes a single `#[test]` is the file.

- `determinism.rs` — the released binary, run twice on the same input, produces the same exit code and the same file contents (modulo timestamps).
- `empty_input.rs` — running the binary against an empty directory does not panic; it produces an explicit "no manifests found" report.
- `idempotence.rs` — a `--fix` or `--pr` mode (where applicable) is idempotent: running it twice does not double-modify and converges to a stable state.

### What's in `qa/adversarial/`

Stuff that shouldn't happen, but might. Each file is a category of "screwed-up input."

- `malformed_manifest.rs` — truncated JSON, BOM-prefixed, nested 100,000 deep, encoding garbage.
- `whitespace.rs` — tabs only, CR only, mixed line endings, empty bytes.
- `unicode.rs` — RTL marks, zero-width joiners, NFC vs NFD normalization, replacement characters.

These run in the normal `cargo test` cycle. Anything that *panics* is a regression.

### What's in `qa/cli/`

Black-box tests against the actual binary, not the source.

- `smoke.sh` — runs each verb in §7. Asserts exit codes match the documented contract (0 = clean, 1 = gate-breach, 2 = error, 3 = panic-rescued). Asserts `--help` is non-empty and lists each verb from §7.
- `subcommands_present.sh` — `grep`s `--help` output for each verb defined in §7. Fails if any are missing.
- `fixtures/sample/` — a tiny but real Node project, Rust project, and Python project that the CLI smoke tests can scan. Reuses the same fixtures as the unit tests.

### What's in `qa/fuzzer/`

Long-form random input testing. Run nightly or weekly, not on every commit.

- `parse_manifest_fuzz.rs` — random-bytes input to `parse_manifest_string`. Asserts:
  - never panics,
  - returns `Ok` or `Err(AppError::Parse | AppError::Invalid)` — nothing else,
  - if it returns `Ok`, the result round-trips back to itself.
- `score_roundtrip_fuzz.rs` — random `Score` arrays. Asserts:
  - composite is in `[0, 100]`,
  - score reorder doesn't change output,
  - adding a `None` outcome never silently drops the dep from the report.

The fuzzers are written in a way that can run as `cargo test` with a `--features fuzzer` flag. They don't depend on any external crate (per §12).

### How to run

```sh
# All functional QA, all invariants, all adversarial, all CLI smoke.
./scripts/qa.sh

# Equivalent:
cargo test --release --bin <service> -- --nocapture
bash qa/cli/smoke.sh
bash qa/cli/subcommands_present.sh

# Long-form fuzzer (weekly):
cargo test --release --features fuzzer -- --ignored
```

### How this matches §2 (≤ 256 LoC per file)

QA files are also ≤ 256 LoC. If a `qa/functional/probe_lookup.rs` would tip 256, split it by *invariant class*: `probe_lookup_known.rs`, `probe_lookup_unknown.rs`, `probe_lookup_failure.rs`. The contract is "every concept in `src/<concept>.rs` gets a `qa/functional/<concept>.rs`, and that file is the canonical home for the functional tests of that concept."

### One anti-pattern to reject in code review

```rust
// DON'T ACCEPT THIS
#[test]
fn runs_without_crashing() {
    let _ = parse_manifest("anything");
}
```

A test that asserts "did not panic" without specifying what the function is supposed to do is a contract-free test. It will pass when the function is broken. It is decoration, not verification. Reject in review. If a reviewer wrote one, ask them what the function is supposed to do, and force the question to be answered in a comment.

### The connection to §4 (First principles)

§4 says every service starts from one question. §13 says every function is verified against its share of that question. Together they mean: the question determines the contract, the contract determines the tests, the tests determine whether the binary deserves to be called a product. A test that doesn't reference the contract is a test that doesn't know what the function is for.
