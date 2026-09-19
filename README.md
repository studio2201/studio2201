# studio2201

[![studio2201 Suite](https://img.shields.io/badge/studio2201-5%2F5%20Verified-2f6f5e?logo=shield)](https://studio2201.com/agents#badges)
[![Release](https://img.shields.io/badge/version-v1.3.2-blue.svg)](https://github.com/studio2201/studio2201/releases)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)

Parent framework and composite security governance gate for the studio2201 ecosystem.

## Why Automated CI Enforcement Is Needed

Software supply-chain attacks and code vulnerabilities have migrated upstream:

- **Upstream Supply-Chain Sabotage**: High-profile incidents like XZ Utils (CVE-2024-3094), SolarWinds, and `colors.js` prove that malicious compromise often enters through dormant dependencies, build-phase injection, or compromised maintainer accounts.
- **AI Vibe-Coding Secrets & SQL Exposure**: AI assistants (Cursor, Claude Code, Copilot) generate code rapidly, but GitGuardian research shows AI-generated commits leak production secrets at more than double human rates (3.2% vs 1.5%), while omitting database Row Level Security (RLS).
- **Post-Quantum Cryptographic Mandates**: Under White House OMB M-26-15 and NIST FIPS 203/204, classical RSA and ECC algorithms must transition to post-quantum standards (ML-KEM, ML-DSA) by 2030 to neutralize "Harvest Now, Decrypt Later" threats.
- **Automated CI Gates vs Manual Discipline**: Human developer discipline inevitably degrades under delivery deadlines. Automated GitHub Actions gates provide non-bypassable, fail-closed enforcement on every pull request before code merges.

## Autonomous Agent Integration

Deploy the unified multi-tool security gate directly through your AI coding assistant or copy the workflow below into your repository.

### Prompt for your AI Agent

Copy and paste this instruction directly to Cursor, Claude Code, Copilot Workspace, or Devin:

```text
Add a GitHub Actions workflow to this repository at .github/workflows/studio2201.yml using studio2201/studio2201@master. Configure it to trigger on pull requests and pushes to master, auditing PR diffs with Snip, dependencies with Vigil, and cryptographic call sites with Aegis. It must output a summary scorecard to $GITHUB_STEP_SUMMARY and fail the build on security violations.
```

### GitHub Actions Workflow

Commit this minimal, zero-maintenance workflow at `.github/workflows/studio2201.yml`:

```yaml
name: studio2201 Security Gate
on:
  pull_request:
    branches: [ master, main ]
  push:
    branches: [ master, main ]
permissions:
  contents: read
jobs:
  security-gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Run studio2201 Multi-Tool Security Gate
        uses: studio2201/studio2201@master
        with:
          tools: 'all'
          fail-on: 'block'
```

## Five Products & Tool Status

| Product | Focus | Tool-Specific Badge | Action / CI | Version |
| :--- | :--- | :--- | :--- | :--- |
| [**Vigil**](vigil/) | Supply-chain dormancy scanner | [![Dormancy](https://img.shields.io/badge/dormancy-healthy-2f6f5e.svg)](vigil/) | [![CI](https://github.com/studio2201/vigil/actions/workflows/ci.yml/badge.svg?branch=master)](vigil/) | `v0.2.8` |
| [**Snip**](snip/) | Vibe-code security gate | [![Vibe-Safe](https://img.shields.io/badge/vibe--safe-SHIP-brightgreen.svg)](snip/) | [![CI](https://github.com/studio2201/snip/actions/workflows/ci.yml/badge.svg?branch=master)](snip/) | `v0.2.7` |
| [**Boneyard**](boneyard/) | Org-wide tech-debt radar | [![Boneyard](https://img.shields.io/badge/boneyard%20index-0%2F100-brightgreen.svg)](boneyard/) | [![CI](https://github.com/studio2201/boneyard/actions/workflows/ci.yml/badge.svg?branch=master)](boneyard/) | `v0.2.7` |
| [**Aegis**](aegis/) | PQC migration SDK & scanner | [![PQC](https://img.shields.io/badge/PQC-Quantum--Safe-blueviolet.svg)](aegis/) | [![CI](https://github.com/studio2201/aegis/actions/workflows/ci.yml/badge.svg?branch=master)](aegis/) | `v0.2.7` |
| [**Proven**](proven/) | PQC-signed supply-chain attestor | [![SLSA](https://img.shields.io/badge/SLSA-Level%203%2B-blue.svg)](proven/) | [![CI](https://github.com/studio2201/proven/actions/workflows/ci.yml/badge.svg?branch=master)](proven/) | `v0.2.7` |
| [**CLI**](cli/) | Unified toolchain & local audit driver | [![Toolchain](https://img.shields.io/badge/toolchain-unified-blue.svg)](cli/) | [![CI](https://github.com/studio2201/cli/actions/workflows/ci.yml/badge.svg?branch=master)](cli/) | `v0.1.4` |

## Core Doctrine & Architecture

| Document | Focus |
|---|---|
| [`DESIGN.md`](DESIGN.md) | Ecosystem architectural design, 6-tool suite & artifact contracts |
| [`RULES.md`](RULES.md) | Immutable engineering constraints (pure `std::` Rust, $\le 256$ LOC, hostile testing) |

**Documentation & Storefront:** https://studio2201.com

## Workstation & Toolchain Installation

Install the unified `studio2201` CLI and toolchain to manage, audit, and upgrade all tools:

```bash
# Install the unified CLI and all tools:
curl -fsSL https://studio2201.com/install.sh | sh -s all

# Manage toolchain and run pre-commit audits:
studio2201 list               # check installed tools & versions
studio2201 check              # run audit suite locally
studio2201 install <tool>     # install a specific tool
studio2201 upgrade all        # upgrade tools to latest release
studio2201 remove <tool>      # remove an installed tool
studio2201 init               # scaffold GitHub Action & agent guardrails
```

---

<div align="center">

[![Necrometer](necrometer.svg)](https://necrometer.dev/?u=studio2201)

</div>
