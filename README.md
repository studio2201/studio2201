# studio2201

[![studio2201 Suite](https://img.shields.io/badge/studio2201-5%2F5%20Verified-2f6f5e?logo=shield)](https://studio2201.com/agents#badges)
[![Release](https://img.shields.io/badge/version-v1.3.9-blue.svg)](https://github.com/studio2201/studio2201/releases)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)

<details>
<summary>
  <a href="https://studio2201.com/agents#badges">
    <img src="https://img.shields.io/badge/studio2201-5%2F5%20Verified-2f6f5e?logo=shield" alt="studio2201 Suite">
  </a> <b>Detailed Governance Scorecard</b>
</summary>

| Tool | Focus | Verdict | Status Badge |
| :--- | :--- | :---: | :---: |
| [**Snip**][u-snip] | Vibe-Code & Secrets Gate | `SHIP` | [![Vibe-Safe][b-snip]][u-snip] |
| [**Vigil**][u-vigil] | Supply-Chain Dormancy | `HEALTHY` | [![Dormancy][b-vigil]][u-vigil] |
| [**Aegis**][u-aegis] | PQC & Post-Quantum Scans | `QUANTUM-SAFE` | [![PQC][b-aegis]][u-aegis] |
| [**Proven**][u-proven] | ML-DSA-65 Attestation | `VERIFIED` | [![SLSA][b-proven]][u-proven] |
| [**Boneyard**][u-boneyard] | Tech-Debt Radar | `0/100 DEBT` | [![Boneyard][b-boneyard]][u-boneyard] |

[u-snip]: https://studio2201.com/snip
[u-vigil]: https://studio2201.com/vigil
[u-aegis]: https://studio2201.com/aegis
[u-proven]: https://studio2201.com/proven
[u-boneyard]: https://studio2201.com/boneyard
[b-snip]: https://img.shields.io/badge/vibe--safe-SHIP-brightgreen.svg
[b-vigil]: https://img.shields.io/badge/dormancy-healthy-2f6f5e.svg
[b-aegis]: https://img.shields.io/badge/PQC-Quantum--Safe-blueviolet.svg
[b-proven]: https://img.shields.io/badge/SLSA-Level%203%2B-blue.svg
[b-boneyard]: https://img.shields.io/badge/boneyard%20index-0%2F100-brightgreen.svg

</details>

Parent framework and composite security governance gate for the studio2201 ecosystem.

## Why Automated CI Enforcement Is Needed

Software supply-chain attacks and code vulnerabilities have migrated upstream:

- **Upstream Supply-Chain Sabotage**: High-profile incidents like XZ Utils
  (CVE-2024-3094), SolarWinds, and `colors.js` prove that malicious compromise
  often enters through dormant dependencies, build-phase injection, or
  compromised maintainer accounts.
- **AI Vibe-Coding Secrets & SQL Exposure**: AI assistants (Cursor, Claude Code,
  Copilot) generate code rapidly, but GitGuardian research shows AI-generated
  commits leak production secrets at more than double human rates (3.2% vs 1.5%),
  while omitting database Row Level Security (RLS).
- **Post-Quantum Cryptographic Mandates**: Under White House OMB M-26-15 and
  NIST FIPS 203/204, classical RSA and ECC algorithms must transition to
  post-quantum standards (ML-KEM, ML-DSA) by 2030 to neutralize "Harvest Now,
  Decrypt Later" threats.
- **Automated CI Gates vs Manual Discipline**: Human developer discipline
  inevitably degrades under delivery deadlines. Automated GitHub Actions gates
  provide non-bypassable, fail-closed enforcement on every pull request before
  code merges.

## Autonomous Agent Integration

Deploy the unified multi-tool security gate directly through your AI coding assistant or copy the workflow below into your repository.

### Prompt for your AI Agent

Copy and paste this instruction directly to Cursor, Claude Code, Copilot Workspace, or Devin:

```text
Add a GitHub Actions workflow to this repository at .github/workflows/studio2201.yml using studio2201/studio2201@v1:
- Triggers: pull_request, push to default branch (master/main), and workflow_dispatch.
- Concurrency: group ${{ github.workflow }}-${{ github.ref }} with cancel-in-progress on pull_request.
- Permissions: contents: read, checkout with fetch-depth: 0.
- Action: run studio2201/studio2201@v1 with tools: 'snip,vigil,aegis' and fail-on: 'block'.
- Artifacts: upload *_report.md using actions/upload-artifact@v4 with if: always().
- Zero False Positives: @v1 provisions Vigil v0.2.11+ (excludes root crates) & Aegis v0.2.10+ (skips comments).
- README Badge: add dynamic Shields.io workflow status badge to top of README.md (replace <owner>/<repo>):
  [![studio2201][badge]][ci]
  [badge]: https://img.shields.io/github/actions/workflow/status/<owner>/<repo>/studio2201.yml?label=studio2201&logo=shield
  [ci]: https://github.com/<owner>/<repo>/actions/workflows/studio2201.yml
```

### GitHub Actions Workflow

Commit this minimal, zero-maintenance workflow at `.github/workflows/studio2201.yml`:

```yaml
name: studio2201 Security Gate
on:
  push:
    branches: [ master, main ]
  pull_request:
    branches: [ master, main ]
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' }}

permissions:
  contents: read

jobs:
  security-gate:
    name: studio2201 Security Gate
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run studio2201 Security Gate
        uses: studio2201/studio2201@v1
        with:
          tools: 'snip,vigil,aegis'
          fail-on: 'block'

      - name: Retain Audit Findings
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: studio2201-audit-findings
          path: |
            *_report.md
          if-no-files-found: ignore
```

## Five Products & Tool Status

| Product | Focus | Tool-Specific Badge | Action / CI | Version |
| :--- | :--- | :--- | :--- | :--- |
| [**Vigil**](vigil/) | Supply-chain dormancy scanner | [![Dormancy][b-vigil]](vigil/) | [![CI][ci-vigil]](vigil/) | `v0.2.11` |
| [**Snip**](snip/) | Vibe-code security gate | [![Vibe-Safe][b-snip]](snip/) | [![CI][ci-snip]](snip/) | `v0.2.9` |
| [**Boneyard**](boneyard/) | Org-wide tech-debt radar | [![Boneyard][b-boneyard]](boneyard/) | [![CI][ci-boneyard]](boneyard/) | `v0.2.9` |
| [**Aegis**](aegis/) | PQC migration SDK & scanner | [![PQC][b-aegis]](aegis/) | [![CI][ci-aegis]](aegis/) | `v0.2.10` |
| [**Proven**](proven/) | PQC-signed supply-chain attestor | [![SLSA][b-proven]](proven/) | [![CI][ci-proven]](proven/) | `v0.2.9` |
| [**CLI**](cli/) | Unified toolchain & local audit driver | [![Toolchain][b-cli]](cli/) | [![CI][ci-cli]](cli/) | `v0.1.9` |

[ci-vigil]: https://github.com/studio2201/vigil/actions/workflows/ci.yml/badge.svg?branch=master
[ci-snip]: https://github.com/studio2201/snip/actions/workflows/ci.yml/badge.svg?branch=master
[ci-boneyard]: https://github.com/studio2201/boneyard/actions/workflows/ci.yml/badge.svg?branch=master
[ci-aegis]: https://github.com/studio2201/aegis/actions/workflows/ci.yml/badge.svg?branch=master
[ci-proven]: https://github.com/studio2201/proven/actions/workflows/ci.yml/badge.svg?branch=master
[ci-cli]: https://github.com/studio2201/cli/actions/workflows/ci.yml/badge.svg?branch=master
[b-cli]: https://img.shields.io/badge/toolchain-unified-blue.svg

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
