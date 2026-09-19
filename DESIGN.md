# studio2201 Architectural Design

**studio2201** is a pure `std::` Rust software suite providing automated security, supply-chain governance, and post-quantum cryptographic validation for modern codebases and AI coding agents.

## 1. Core Architecture Principles

- **Zero Crates.io Dependencies:** Built strictly with pure `std::` Rust. Zero external supply-chain attack surfaces.
- **Strict File Bounds (<= 256 LOC):** Every source file, test, and workflow is bounded, forcing modularity and cognitive clarity.
- **Fail-Closed Verification:** Tools exit with code `0` on verified clean, and exit code `1` on security, policy, or supply-chain violations.
- **Zero-Root Operation:** Installs and operates entirely within unprivileged user space (`~/.local/bin`).

## 2. The studio2201 Product Suite

Each tool solves one specific integrity challenge:

1. **Snip (Vibe-Code Security Gate):** Audits AI-generated diffs for hardcoded credentials, missing database Row Level Security (RLS), and permissive CORS.
2. **Vigil (Supply-Chain Dormancy Scanner):** Evaluates project manifests (`Cargo.lock`, `package.json`, `pyproject.toml`, `go.mod`) for abandoned dependencies.
3. **Aegis (PQC Migration SDK & Scanner):** Identifies quantum-vulnerable classical cryptography (RSA, ECDSA) and plans migrations to NIST FIPS 203/204 standards (ML-KEM, ML-DSA).
4. **Proven (Supply-Chain Attestor):** Computes Merkle roots and signs build artifacts with post-quantum ML-DSA-65 attestations (SLSA Level 3+).
5. **Boneyard (Tech-Debt Radar):** Evaluates multi-repo catalogs to score technical debt (0–100 Boneyard Index) and emit remediation budgets in repo-weeks.
6. **studio2201 CLI (Toolchain & Audit Manager):** Unified single-binary frontend for zero-root installation, upgrades, and local pre-commit checks (`studio2201 check`).
7. **Canary (Negative Verification Testbed):** Intentionally broken reference repository asserting that defensive gates reliably catch flaws and fail closed.

## 3. Product Framework

The full doctrine and engineering specifications are detailed across the 19 sections in `PRODUCTS/index.md`.
