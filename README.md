# studio2201

<div align="center">

[![Release](https://img.shields.io/badge/version-v1.4.4-blue.svg)](https://github.com/studio2201/studio2201/releases)
[![GNOME Suite](https://img.shields.io/badge/GNOME%2045--50-6%20Wayland%20Extensions-0b6e87)](https://studio2201.com/#gnome)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![Sponsor: Tuition, Not Coffee](https://img.shields.io/badge/Sponsor-Tuition%2C%20Not%20Coffee%20%F0%9F%8E%93-2ec27e)](https://ubermetroid.github.io/tuition-not-coffee)

| Security Pillar | Verification Badge |
| :--- | :---: |
| **Platform Standard** | [![secured by studio2201][b-studio]][u-home] |
| **Credential Defense** | [![snip][b-snip]][u-snip] |
| **Supply Chain Surface** | [![vigil][b-vigil]][u-vigil] |
| **Post-Quantum Cryptography** | [![aegis][b-aegis]][u-aegis] |
| **Build Provenance & SLSA** | [![proven][b-proven]][u-proven] |
| **Repository Governance** | [![boneyard][b-boneyard]][u-boneyard] |

[b-studio]: https://img.shields.io/badge/secured%20by-studio2201-2f6f5e?logo=shield
[u-home]: https://studio2201.com
[b-snip]: https://img.shields.io/badge/snip-0%20secrets-2f6f5e?logo=shield
[u-snip]: https://studio2201.com/snip
[b-vigil]: https://img.shields.io/badge/vigil-0%20dependencies-2f6f5e?logo=shield
[u-vigil]: https://studio2201.com/vigil
[b-aegis]: https://img.shields.io/badge/aegis-PQC%20compliant-2f6f5e?logo=shield
[u-aegis]: https://studio2201.com/aegis
[b-proven]: https://img.shields.io/badge/proven-ML--DSA--65%20verified-2f6f5e?logo=shield
[u-proven]: https://studio2201.com/proven
[b-boneyard]: https://img.shields.io/badge/boneyard-maintained-2f6f5e?logo=shield
[u-boneyard]: https://studio2201.com/boneyard

</div>

Parent framework, composite GitHub Actions security gate, and ecosystem index for **studio2201** — spanning **Wayland GNOME Shell Desktop Extensions**, **Zero-Dependency Rust CI/CD Security Actions**, and **Static Web Portals** (&le; 256 LOC per file).

---

## 01 · GNOME Shell Desktop Suite (Wayland 45–50)

Six zero-subprocess, Wayland-native GNOME Shell extensions with one-click **Sync Color Across Suite** accent borders and `shexli` 0-warning EGO verification:

| Extension | Role | UUID | Repository & Microsite | Version |
| :--- | :--- | :--- | :--- | :--- |
| [**Kelp**](https://github.com/studio2201/kelp) | Auto-Tiler (Octopus / Seahorse / Otter) | `kelp@studio2201.github.io` | [Repo](https://github.com/studio2201/kelp) · [Website](https://studio2201.github.io/kelp/) | `v2.3.0` |
| [**Reef**](https://github.com/studio2201/reef) | Intelligent Dock & Panel | `reef@studio2201.github.io` | [Repo](https://github.com/studio2201/reef) · [Website](https://studio2201.github.io/reef/) | `v1.3.0` |
| [**Tide**](https://github.com/studio2201/tide) | Super & `Super+Enter` App Launcher | `tide@studio2201.github.io` | [Repo](https://github.com/studio2201/tide) · [Website](https://studio2201.github.io/tide/) | `v1.3.0` |
| [**Coral**](https://github.com/studio2201/coral) | Alphabetical App Grid & Auto-Folders | `coral@studio2201.github.io` | [Repo](https://github.com/studio2201/coral) · [Website](https://studio2201.github.io/coral/) | `v1.6.0` |
| [**Seabed**](https://github.com/studio2201/seabed) | Compositor-Native Desktop Icons | `seabed@studio2201.github.io` | [Repo](https://github.com/studio2201/seabed) · [Website](https://studio2201.github.io/seabed/) | `v1.6.0` |
| [**Shoal**](https://github.com/studio2201/shoal) | AppIndicator, SNI & DBusMenu Tray | `shoal@studio2201.github.io` | [Repo](https://github.com/studio2201/shoal) · [Website](https://studio2201.github.io/shoal/) | `v1.3.0` |

---

## 02 · GitHub CI/CD Security Actions & Toolchain (Pure `std::` Rust)

| Product | Focus | Tool-Specific Badge | Action / CI | Version |
| :--- | :--- | :--- | :--- | :--- |
| [**Vigil**](vigil/) | Supply-chain dormancy scanner | [![Dormancy][b-vigil]](vigil/) | [![CI][ci-vigil]](vigil/) | `v0.2.13` |
| [**Snip**](snip/) | Vibe-code security gate | [![Vibe-Safe][b-snip]](snip/) | [![CI][ci-snip]](snip/) | `v0.2.11` |
| [**Boneyard**](boneyard/) | Org-wide tech-debt radar | [![Boneyard][b-boneyard]](boneyard/) | [![CI][ci-boneyard]](boneyard/) | `v0.2.12` |
| [**Aegis**](aegis/) | PQC migration SDK & scanner | [![PQC][b-aegis]](aegis/) | [![CI][ci-aegis]](aegis/) | `v0.2.12` |
| [**Proven**](proven/) | PQC-signed supply-chain attestor | [![SLSA][b-proven]](proven/) | [![CI][ci-proven]](proven/) | `v0.2.11` |
| [**CLI**](cli/) | Unified toolchain & local audit driver | [![Toolchain][b-cli]](cli/) | [![CI][ci-cli]](cli/) | `v0.1.13` |

[ci-vigil]: https://github.com/studio2201/vigil/actions/workflows/ci.yml/badge.svg?branch=master
[ci-snip]: https://github.com/studio2201/snip/actions/workflows/ci.yml/badge.svg?branch=master
[ci-boneyard]: https://github.com/studio2201/boneyard/actions/workflows/ci.yml/badge.svg?branch=master
[ci-aegis]: https://github.com/studio2201/aegis/actions/workflows/ci.yml/badge.svg?branch=master
[ci-proven]: https://github.com/studio2201/proven/actions/workflows/ci.yml/badge.svg?branch=master
[ci-cli]: https://github.com/studio2201/cli/actions/workflows/ci.yml/badge.svg?branch=master
[b-cli]: https://img.shields.io/badge/toolchain-unified-blue.svg

### GitHub Actions Workflow & Autonomous Agent Integration

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

### Workstation CLI Installation

```bash
curl -fsSL https://studio2201.com/install.sh | sh -s all
studio2201 list               # check installed tools & versions
studio2201 check              # run audit suite locally
studio2201 upgrade all        # upgrade tools to latest release
studio2201 init               # scaffold GitHub Action & agent guardrails
```

---

## 03 · Webpages, Portals & Core Doctrine

| Resource | Focus |
|---|---|
| [**`studio2201.com`**](https://studio2201.com) | Main organization portal, product specs, [Agent Guide](https://studio2201.com/agents) & [Canary Showcase](https://studio2201.com/canary) |
| [**`Tuition, Not Coffee`**](https://ubermetroid.github.io/tuition-not-coffee) | Community sponsorship supporting our daughter's college tuition fund |
| [`DESIGN.md`](DESIGN.md) | Ecosystem architectural design & artifact contracts |
| [`RULES.md`](RULES.md) | Immutable engineering constraints (pure `std::` Rust, $\le 256$ LOC, hostile testing) |
