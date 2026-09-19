# studio2201

[![secured by studio2201](https://img.shields.io/badge/secured%20by-studio2201-2f6f5e?logo=shield)](https://studio2201.com)
[![snip][badge-snip]][action-snip]
[![vigil][badge-vigil]][action-vigil]
[![aegis][badge-aegis]][action-aegis]
[![proven][badge-proven]][action-proven]
[![boneyard][badge-boneyard]][action-boneyard]
[![Release](https://img.shields.io/badge/version-v1.3.11-blue.svg)](https://github.com/studio2201/studio2201/releases)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)

[badge-snip]: https://img.shields.io/github/actions/workflow/status/studio2201/studio2201/snip.yml?label=snip&logo=shield
[action-snip]: https://github.com/studio2201/studio2201/actions/workflows/snip.yml
[badge-vigil]: https://img.shields.io/github/actions/workflow/status/studio2201/studio2201/vigil.yml?label=vigil&logo=shield
[action-vigil]: https://github.com/studio2201/studio2201/actions/workflows/vigil.yml
[badge-aegis]: https://img.shields.io/github/actions/workflow/status/studio2201/studio2201/aegis.yml?label=aegis&logo=shield
[action-aegis]: https://github.com/studio2201/studio2201/actions/workflows/aegis.yml
[badge-proven]: https://img.shields.io/github/actions/workflow/status/studio2201/studio2201/proven.yml?label=proven&logo=shield
[action-proven]: https://github.com/studio2201/studio2201/actions/workflows/proven.yml
[badge-boneyard]: https://img.shields.io/github/actions/workflow/status/studio2201/studio2201/boneyard.yml?label=boneyard&logo=shield
[action-boneyard]: https://github.com/studio2201/studio2201/actions/workflows/boneyard.yml

<details>
<summary><b>Executive Assurance Scorecard</b> (EO 14028 · NIST SP 800-218 · OMB M-26-15)</summary>

| Tool | Mandate / Standard | Assurance Metric | Status |
| :--- | :--- | :--- | :---: |
| [**Snip**][u-snip] | EO 14028 §4 (Credential Defense) | `0 secrets` | [![snip][m-snip]][u-snip] |
| [**Vigil**][u-vigil] | NIST SP 800-218 (Supply Surface) | `0 dependencies` | [![vigil][m-vigil]][u-vigil] |
| [**Aegis**][u-aegis] | OMB M-26-15 (Post-Quantum Crypto) | `PQC compliant` | [![aegis][m-aegis]][u-aegis] |
| [**Proven**][u-proven] | NIST FIPS 204 (SLSA Level 3+) | `ML-DSA-65 verified` | [![proven][m-proven]][u-proven] |
| [**Boneyard**][u-boneyard] | Asset Ownership (Zombie Defense) | `maintained` | [![boneyard][m-boneyard]][u-boneyard] |

[u-snip]: https://studio2201.com/snip
[u-vigil]: https://studio2201.com/vigil
[u-aegis]: https://studio2201.com/aegis
[u-proven]: https://studio2201.com/proven
[u-boneyard]: https://studio2201.com/boneyard
[m-snip]: https://img.shields.io/badge/snip-0%20secrets-2f6f5e
[m-vigil]: https://img.shields.io/badge/vigil-0%20dependencies-2f6f5e
[m-aegis]: https://img.shields.io/badge/aegis-PQC%20compliant-2f6f5e
[m-proven]: https://img.shields.io/badge/proven-ML--DSA--65%20verified-2f6f5e
[m-boneyard]: https://img.shields.io/badge/boneyard-maintained-2f6f5e

</details>

Parent framework and composite security governance gate for the studio2201 ecosystem.

## Why Automated CI Enforcement Is Needed

Software supply-chain attacks and code vulnerabilities have migrated upstream:
- **LLM-generated credential exposure**: AI coding tools leak API keys at more than double human rates.
- **Unmaintained dependencies**: Over 80% of open-source components carry dormant risks.
- **Cryptographic obsolescence**: Classical algorithms face imminent quantum decryption (OMB M-26-15).
- **Automated enforcement**: Security discipline degrades under deadlines. Automated GitHub Actions gates
  provide non-bypassable, fail-closed enforcement on every pull request before code merges.

## Autonomous Agent Integration

Deploy dedicated security workflows directly through your AI coding assistant:

### Prompt for your AI Agent

Copy and paste this instruction directly to Cursor, Claude Code, Copilot Workspace, or Devin:

```text
Add 5 dedicated GitHub Actions workflows to this repository (.github/workflows/<tool>.yml):
- snip.yml (name: snip, tools: 'snip' — diffs, secrets, Supabase RLS)
- vigil.yml (name: vigil, tools: 'vigil' — dependency dormancy, excludes root crates)
- aegis.yml (name: aegis, tools: 'aegis' — post-quantum crypto, skips comments)
- proven.yml (name: proven, tools: 'proven' — ML-DSA-65 attestations)
- boneyard.yml (name: boneyard, tools: 'boneyard' — tech-debt radar)
All workflows trigger on pull_request, push to master/main, and workflow_dispatch,
use studio2201/studio2201@v1 with fail-on: 'block', and retain *_report.md.
Add 1 dynamic Shields.io workflow status badge per tool to README.md (replace <owner>/<repo>):
[![snip][b-snip]][ci-snip]
[b-snip]: https://img.shields.io/github/actions/workflow/status/<owner>/<repo>/snip.yml?label=snip&logo=shield
[ci-snip]: https://github.com/<owner>/<repo>/actions/workflows/snip.yml
(Repeat identical pattern for vigil, aegis, proven, boneyard)
```

### GitHub Actions Workflow

Reference template for `.github/workflows/<tool>.yml` (replace `<tool>` with snip, vigil, aegis, proven, boneyard):

```yaml
name: snip
on:
  push: { branches: [ master, main ] }
  pull_request: { branches: [ master, main ] }
  workflow_dispatch:
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' }}
permissions: { contents: read }
jobs:
  snip:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { fetch-depth: 0 }
      - uses: studio2201/studio2201@v1
        with: { tools: 'snip', fail-on: 'block' }
      - uses: actions/upload-artifact@v4
        if: always()
        with: { name: snip-report, path: snip_report.md, if-no-files-found: ignore }
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
