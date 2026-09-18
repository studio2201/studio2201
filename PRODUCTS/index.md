# PRODUCTS/ — studio2201 product framework

This directory replaces the single-file `PRODUCTS.md`. One section per file,
≤ 256 LoC per file. The full framework is the concatenation of these files.

## Sections

| # | Section | File |
|---|---|---|
| §0 | One question per product | [00-question.md](00-question.md) |
| §1 | Rust, and only Rust | [01-rust.md](01-rust.md) |
| §2 | The 256-line rule | [02-lines.md](02-lines.md) |
| §3 | Zero trust at every layer | [03-zero-trust.md](03-zero-trust.md) |
| §4 | First principles | [04-first-principles.md](04-first-principles.md) |
| §5 | Systems thinking | [05-systems-thinking.md](05-systems-thinking.md) |
| §6 | Blue ocean | [06-blue-ocean.md](06-blue-ocean.md) |
| §7 | Functional splits | [07-functional-splits.md](07-functional-splits.md) |
| §8 | Naming | [08-naming.md](08-naming.md) |
| §9 | Substrate (openOODA) | [09-substrate.md](09-substrate.md) |
| §10 | Release cadence | [10-release-cadence.md](10-release-cadence.md) |
| §11 | What gets inherited | [11-inherited.md](11-inherited.md) |
| §12 | From scratch | [12-from-scratch.md](12-from-scratch.md) |
| §13 | Functional QA | [13-functional-qa.md](13-functional-qa.md) |
| §14 | Functional edge cases | [14-edge-cases.md](14-edge-cases.md) |
| §15 | Threat model | [15-threat-model.md](15-threat-model.md) |
| §16 | Reproducible builds | [16-reproducible-builds.md](16-reproducible-builds.md) |
| §17 | Security disclosure | [17-security-disclosure.md](17-security-disclosure.md) |
| §18 | Performance budgets | [18-performance-budgets.md](18-performance-budgets.md) |

## Per-repo artifacts required by §15–§18

Each of the five services (`vigil`, `proven`, `aegis`, `snip`, `boneyard`)
ships the same six files; only the content varies:

| Artifact | Required by |
|---|---|
| `docs/threat-model.md` | §15 |
| `SECURITY.md` | §17 |
| `tools/dev/repro.sh` | §16 |
| `tools/perf/budget.md` | §18 |
| `tools/perf/bench.rs` (as `#[test]`) | §18 |
| `CHANGES.md` | All four (audit trail) |

The doctrine-level substrate matrix is at [`../COMPATIBILITY.md`](../COMPATIBILITY.md).

## Reading order

If you're new to the framework: §0 → §1 → §3 → §11 → §12 → §13.
If you're shipping a service: §15 → §16 → §17 → §18 first.
If you're auditing: §13 → §14 → §15 → §17 → §18 in that order.
