# Section 16 · Reproducible builds — same host, same rustc, same `Cargo.lock`, byte-identical binary


**Reproducibility is `(host, rustc, Cargo.lock)`-stable, not "machine-stable forever."** Per the user's standing rule — "use latest for rust and other tools" — there is no `rust-toolchain.toml`, no `.cargo/config.toml` rustc override, and no version pin anywhere in the framework. The contract is: given the same host triple, the same `rustc stable` minor version, and the same `Cargo.lock`, `tools/dev/repro.sh` produces a binary whose SHA-256 matches the recorded baseline. New Rust releases get new baseline files; old ones stay in history.

### Rule

Each service ships `tools/dev/repro.sh` (≤ 40 LoC, executable, `bash`). The script, in order:

1. Prints `rustc --version` and the host triple. Exits non-zero if `rustc` reports a stable minor version below the floor (`1.96`).
2. Runs `cargo build --locked --release --offline`. Exits non-zero if the build fails or `--offline` cannot resolve the lockfile against the local cache.
3. SHA-256s the produced binary at `target/release/<service>`.
4. Resolves the baseline file at `tools/dev/baselines/<host-triple>__rustc-X.Y.Z.sha256` (where `X.Y.Z` is the `rustc --version` minor.patch) and compares. Exits non-zero on mismatch.
5. On first run after a brand-new `tools/dev/baselines/` directory (no file for the current `(host, rustc)` pair), the script prints the new SHA and writes the baseline file. Subsequent runs assert equality.

That's the entire reproducibility story. No Docker, no Nix, no Bazel.

### Why no toolchain pin

§12 (from scratch) requires no Cargo dependencies. A pinned `rust-toolchain.toml` is a Rust-side pin that violates the user's "always latest" rule. The trade-off is acknowledged and explicit: a new stable Rust release produces a new baseline; old baselines are kept as a history of "what this binary was at `rustc 1.96.1`." Reproducibility here means: **you can re-derive what we built at any point in time**, not "the same SHA forever."

The per-host baseline layout (one file per `(host, rustc-version)` pair):

```
tools/dev/baselines/
├── x86_64-unknown-linux-gnu__rustc-1.96.1.sha256
├── x86_64-unknown-linux-gnu__rustc-1.97.0.sha256     # future release
├── aarch64-apple-darwin__rustc-1.96.1.sha256
├── aarch64-apple-darwin__rustc-1.97.0.sha256         # future release
└── ...
```

The script picks the file matching the current `(uname -m, rustc --version)`. If the file does not exist (first run after a stable Rust upgrade), it is created and the script exits 0 with a "baseline recorded" note. If the file exists and the SHA differs, the script exits non-zero with the diff printed.

### Other tools — also "latest"

The "use latest" rule applies to everything the framework touches:

| Tool | How version is determined |
|---|---|
| `rustc`, `cargo` | From `rustup default stable` or system `$PATH`. No pin. |
| `gcc` / `clang` | Whatever the host system provides. Used only when a vendored C bit (§9) is linked. |
| `bash`, `git`, `gh`, `curl`, `jq`, `python3` | System versions. No pin. |
| `node`, `npm` | **Not used** by any service. §12 forbids them. |

A binary built on `gcc 13.2` today will not match a binary built on `gcc 14.0` tomorrow. The baseline file records the C compiler version too, alongside the rustc version. Vendoring decisions that bring in C code must update §16's baseline schema.

### Contract clauses

| Clause | What it requires |
|---|---|
| **C1** | **No version pinning.** No `rust-toolchain.toml`, no `[toolchain]` channel in any `Cargo.toml`, no `.cargo/config.toml` `rustc` override. The script uses whatever `rustup default stable` (or system cargo) provides. |
| **C2** | `Cargo.lock` is **committed** at the repo root for every service. `repro.sh` uses `cargo build --locked` to refuse network fetches. |
| **C3** | `repro.sh` produces the binary, SHA-256s it, prints `rustc --version`, the host triple, and the SHA to stdout. Output is machine-parseable line-oriented (`field=value` or `field: value`). |
| **C4** | Per-host baselines live at `tools/dev/baselines/<host-triple>__rustc-X.Y.Z.sha256`. Old baselines are **never deleted**. They are the audit trail. |
| **C5** | On baseline mismatch, `repro.sh` exits non-zero with the recorded baseline SHA and the actual SHA both printed. No "force" flag; the developer must update the baseline intentionally. |
| **C6** | If `rustup` is unavailable on the host, `repro.sh` falls back to system `cargo` from `$PATH`. If neither `rustup` nor `cargo` is found, the script prints a one-line install hint and exits non-zero. **No hard dep on `rustup`.** |

### Worked example — `tools/dev/repro.sh` for Vigil (sketch)

```bash
#!/usr/bin/env bash
# repro.sh — build the binary and verify it matches the baseline.
# Honors §16: no toolchain pin; baseline is (host, rustc-version)-keyed.
set -euo pipefail

FLOOR_MINOR="96"  # rustc ≥ 1.96

if ! command -v rustc >/dev/null 2>&1; then
  echo "error: rustc not found. Install via rustup: https://rustup.rs" >&2
  exit 2
fi

RUSTC_VER="$(rustc --version | awk '{print $2}')"   # e.g. 1.96.1
HOST_TRIPLE="$(rustc -vV | sed -n 's|host: ||p')"

MINOR="${RUSTC_VER#*.}"; MINOR="${MINOR%%.*}"
if [ "$MINOR" -lt "$FLOOR_MINOR" ]; then
  echo "error: rustc $RUSTC_VER below floor 1.$FLOOR_MINOR" >&2; exit 3
fi

# Build offline, locked
cargo build --locked --release --offline

BIN="target/release/vigil"
[ -f "$BIN" ] || { echo "error: $BIN not produced" >&2; exit 4; }

SHA="$(sha256sum "$BIN" | awk '{print $1}')"
echo "rustc: $RUSTC_VER"
echo "host:  $HOST_TRIPLE"
echo "sha256: $SHA"

BASELINE_DIR="tools/dev/baselines"
BASELINE_FILE="${BASELINE_DIR}/${HOST_TRIPLE}__rustc-${RUSTC_VER}.sha256"
mkdir -p "$BASELINE_DIR"

if [ ! -f "$BASELINE_FILE" ]; then
  echo "$SHA  $BIN" > "$BASELINE_FILE"
  echo "baseline recorded: $BASELINE_FILE"
  exit 0
fi

EXPECTED="$(awk '{print $1}' "$BASELINE_FILE")"
if [ "$EXPECTED" != "$SHA" ]; then
  echo "MISMATCH:" >&2
  echo "  expected: $EXPECTED" >&2
  echo "  actual:   $SHA" >&2
  exit 5
fi

echo "reproducible: OK"
```

The full per-service script is parameterized by binary name (vigil / proven / aegis / snip / boneyard). The same skeleton ships five times; only the binary name changes.

### Connection to §3 (Zero Trust), §12 (From Scratch), §13 (QA), §14 (Edge Cases)

- **§3 (zero trust).** Reproducibility is the operational expression of §3 in the build pipeline. "Verify everything" includes "verify that the bytes you ship are the bytes the source produces." If the SHA matches, the supply chain from source commit to `target/release/<service>` is intact.
- **§12 (zero deps).** `repro.sh` succeeds with **no network and no crates.io** because §12 keeps `Cargo.toml` dep-free. The whole point of "no deps" is that `cargo build --offline --locked` is always satisfiable. §16 is the verification that §12's promise actually holds.
- **§13 (functional QA).** `repro.sh` is the build-time functional test: "given the same inputs, the build pipeline produces the same outputs." If §13 says every function has a test, §16 says the *entire binary* has a test.
- **§14 (edge cases).** The edge cases of §16 are exactly the boundary inputs to `repro.sh`: rustc at the floor version (1.96), rustc at the next major, rustc on a host with no baseline yet, rustc on a host where the binary is missing, `--offline` failing because the lockfile references an absent crate. Each boundary gets a smoke run during onboarding a new service.

### How to audit §16

```bash
# A16-A: no toolchain pin in any service
for s in vigil proven aegis snip boneyard; do
  test ! -f studio2201/$s/rust-toolchain.toml \
    || echo "PINNED: studio2201/$s/rust-toolchain.toml"
done

# A16-B: repro.sh is syntactically valid bash on all 5
for s in vigil proven aegis snip boneyard; do
  bash -n studio2201/$s/tools/dev/repro.sh || echo "BAD SYNTAX: $s"
done

# A16-C: every Cargo.lock is committed
for s in vigil proven aegis snip boneyard; do
  test -f studio2201/$s/Cargo.lock || echo "MISSING Cargo.lock: $s"
done
```

A failure in any of the three lines is a §16 contract violation.

### One-line policy

> Reproducibility is a property of the build pipeline, not a property of the toolchain — and we own the pipeline.
