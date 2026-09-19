# studio2201 Architectural Design & Framework

**studio2201** is a pure `std::` Rust software ecosystem providing automated security, supply-chain governance, and post-quantum cryptographic validation for modern software supply chains, continuous integration (CI) gates, and autonomous AI coding agents.

---

## 1. Core Architectural Tenets

1. **Zero Crates.io Dependencies (Pure `std::` Rust):**
   Every tool is implemented strictly using the Rust standard library (`std::*`). No third-party crates, no hidden transitive supply chains, zero CVE surface from external dependencies.
2. **Strict Cognitive Bounds ($\le 256$ LOC):**
   Every source file, markdown document, shell script, and workflow file is strictly bounded to at most 256 lines of code. This prevents architectural sprawl and forces modular, composable designs.
3. **Fail-Closed Verification:**
   Security gates enforce fail-closed semantics. Any unparseable payload, ambiguous state, or missing policy file terminates execution with a blocking verdict rather than an insecure bypass.
4. **Zero-Root Operation:**
   All binaries install, configure, and execute in unprivileged user space following the Linux XDG Base Directory Specification (`~/.local/bin`, `~/.config/studio2201/`). Zero `sudo` requirements.

---

## 2. The 6-Tool Security Suite & Canary Testbed

The ecosystem consists of six purpose-built binaries and an adversarial validation harness:

### 1. Snip (`snip`) — Vibe-Code Security Gate
- **Focus:** AI-generated code diffs, secrets exposure, and web authorization boundaries.
- **Engine:** Inspects unified diffs (`git diff`) on stdin or target patches. Evaluates diff chunks for exposed API keys, JWTs, cloud credentials, missing database Row Level Security (RLS) policies, and overly permissive CORS configurations.
- **Interfaces:** Headless CLI (`snip --format json`), git pre-commit hook (`git diff --staged | snip`), and Model Context Protocol daemon (`snip serve --mcp`).

### 2. Vigil (`vigil`) — Supply-Chain Dormancy Scanner
- **Focus:** Ecosystem dependency risk and upstream abandonment.
- **Engine:** Scans project lockfiles (`Cargo.lock`, `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`). Scores dependency packages based on release timestamps and upstream inactivity.
- **Outputs:** Emits dependency dormancy scorecards, `SUPPLY-CHAIN.md`, and dynamic SVG status badges.

### 3. Aegis (`aegis`) — Post-Quantum Cryptography Migration SDK
- **Focus:** Quantum vulnerability remediation under NIST FIPS 203/204 and OMB M-26-15.
- **Engine:** Scans abstract syntax trees and call sites for classical cryptography (RSA, ECDSA, DH). Generates structured transition plans to NIST post-quantum standards (ML-KEM, ML-DSA).
- **Subcommands:** `scan`, `plan`, `shim`, `doctor`.

### 4. Proven (`proven`) — Supply-Chain Attestor & Provenance
- **Focus:** SLSA Level 3+ build integrity and artifact provenance.
- **Engine:** Computes deterministic SHA-256 Merkle trees across build outputs and emits machine-verifiable attestations cryptographically signed with post-quantum ML-DSA-65 signatures.
- **Subcommands:** `hash`, `sign`, `verify`, `doctor`.

### 5. Boneyard (`boneyard`) — Multi-Repository Tech-Debt Radar
- **Focus:** Architectural decay and organizational technical debt budgets.
- **Engine:** Evaluates repository catalogs (`hall.json`, `catalog.json`), calculating a 0–100 Boneyard Debt Index based on file complexity, orphan dependencies, and documentation staleness. Emits remediation effort budgets in repo-weeks.
- **Subcommands:** `scan`, `enrich`, `budget`, `doctor`.

### 6. studio2201 CLI (`studio2201`) — Unified Driver & Toolchain Manager
- **Focus:** Developer ergonomics, toolchain lifecycle, and local pre-commit audits.
- **Engine:** Single binary controlling discovery, zero-root installation, upgrades, and local composite checks (`studio2201 check`) across all five tools. Gracefully skips checks when target manifests are absent.
- **Subcommands:** `check`, `install`, `remove`, `upgrade`, `list`, `init`.

### 7. Canary (`canary`) — Negative Verification Testbed
- **Focus:** Defensive validation and adversarial challenge verification.
- **Design:** Intentionally flawed reference codebase containing synthetic secret leaks, abandoned dependencies, quantum-fragile algorithms, and unsigned blobs. Used in CI (`canary/demo.sh`) to verify that all five tools fail closed.

---

## 3. Tri-State Exit Code Standard

Every tool across the studio2201 organization adheres to an identical, deterministic exit code contract for headless and CI/CD operation:

| Exit Code | Classification | Meaning & Behavioral Semantics |
| :---: | :--- | :--- |
| **`0`** | **Pass / Clean / Verified** | Target artifact is compliant. No security violations, dormant dependencies, secrets, or debt breaches detected. CI builds proceed. |
| **`1`** | **Policy Block / Violation / Fail-Closed** | Target artifact violated security or governance policy (e.g. secret found, unmaintained package, classical crypto in strict mode). Build fails closed. |
| **`2`** | **CLI / I/O / Argument Error** | Command invocation error: invalid flags, missing required files, bad file descriptors, unparseable input. Differentiates bad syntax from a security block. |

### Machine-First Flags
All binaries implement standard POSIX flags:
- `-h, --help` and `help`: Show context-aware command usage.
- `-V, --version` and `version`: Print semantic version string.
- `-q, --quiet`: Suppress diagnostic output; emit only exit code or payload.
- `-v, --verbose`: Emit detailed execution traces and diagnostics to stderr.
- `-o, --output <PATH>`: Direct output payload to specified file.
- `-f, --format <FMT>`: Select output encoding (`text`, `json`, `markdown`).
- `doctor`: Comprehensive binary integrity and environment health verification.

---

## 4. Per-Repository Artifact Contracts

To guarantee uniformity across all repositories in the studio2201 ecosystem, each tool repository must implement and maintain five standard root contracts:

1. **`docs/threat-model.md` (Threat Model & Boundary Analysis):**
   Formal threat model detailing trust boundaries, parser attack vectors, untrusted input handling, and security assumptions.
2. **`SECURITY.md` (Vulnerability Disclosure):**
   Public vulnerability reporting process, coordinated disclosure policy, cryptographic contact protocol, and response time SLAs.
3. **`tools/dev/repro.sh` (Deterministic Build Verification):**
   Executable shell script verifying bit-for-bit reproducible compilation across clean containers, asserting SHA-256 binary invariance.
4. **`tools/perf/bench.rs` (Performance Budget Micro-Benchmarks):**
   In-tree latency and throughput micro-benchmarks executing under `cargo test --release -- --nocapture`, validating that execution times strictly respect sub-millisecond budgets.
5. **`action.yml` (Native Composite GitHub Action):**
   Composite action enabling sub-2-second installation and direct execution in CI pipelines, emitting structured Markdown scorecards to `$GITHUB_STEP_SUMMARY`.

---

## 5. Deployment & Enforcement Channels

- **Workstation:** Managed via `~/.local/bin/studio2201`, running pre-commit hooks.
- **CI/CD:** Composite GitHub Action `uses: studio2201/studio2201@master`.
- **Autonomous Agents:** Machine CLI and Model Context Protocol (`snip serve --mcp`).
