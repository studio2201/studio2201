# Changelog — studio2201 doctrine

All notable changes to the studio2201 product framework are documented here.
Format: [Keep a Changelog](https://keepachangelog.com/) 1.1.0.
This project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- (placeholder — next iteration's framework changes land here)

## [0.1.1] — 2026-09-17

### Added
- **PRODUCTS/ v2**: monolithic `PRODUCTS.md` (705 LoC) split into a directory
  with one section per file, each ≤ 256 LoC. 19 sections + `index.md` entry
  point. Adds §15 (Threat Model), §16 (Reproducible Builds),
  §17 (Security Disclosure), §18 (Performance Budgets) to the prior §0–§14.
- **COMPATIBILITY.md** (21 LoC): substrate matrix across the five services
  (seance, opm, bb, oodar-sec-pqc, oodar-sec-landlock).
- Per-service §15–§18 artifacts for vigil, proven, aegis, snip, boneyard:
  `docs/threat-model.md`, `SECURITY.md`, `tools/dev/repro.sh`,
  `tools/perf/budget.md`, `tools/perf/bench.rs`, `CHANGES.md`.

### Changed
- `PRODUCTS.md` retired (was 705 LoC single file, now split).
- Per-repo audit gates A0–A13 added; 11 PASS, 2 SKIPPED (A6/A7 deferred
  until services have `Cargo.toml` + `src/`).

### Notes
- Toolchain policy: latest stable rustc, no pinning (per user rule).
- §12 zero-deps maintained everywhere; only `std::time` for the bench.
- Pre-1.0.0 doctrine: GHSA-only security advisories for all five services.

## [0.1.0] — 2026-09-16

### Added
- Initial 14-section framework (`PRODUCTS.md`, 705 LoC) covering:
  §0 one question per product, §1 Rust, §2 the 256-line rule, §3 zero
  trust, §4 first principles, §5 systems thinking, §6 blue ocean,
  §7 functional splits, §8 naming, §9 substrate (openOODA),
  §10 release cadence, §11 inherited from parent doctrine,
  §12 from scratch (no crates.io), §13 functional QA, §14 functional
  edge cases.
- Parent doctrine: RULES.md, OODA.md, PROBE.md, DESIGN.md.
