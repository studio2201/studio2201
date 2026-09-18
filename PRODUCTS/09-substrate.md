# Section 9 · Pure Rust — no external substrate, no runtime library, just `std::`


**The five services are pure Rust projects.** They have no external substrate, no shared runtime library, no linking against any non-std code. Everything they do is built on `std::*` plus a small amount of vendored single-file code where the spec is small and stable. There is no `seance`, no `opm`, no `bb`, no `oodar-sec-*`. The Rust compiler and `std` *are* the substrate.

### Rule

A service's `Cargo.toml` has no `[dependencies]` section and links no `extern "C"` against an external library. Every binary builds with `cargo build --release --offline` and produces a working tool using only what ships with the rustc the user has installed. No substrate directory is checked in or referenced.

### Why this section exists (legacy note)

Earlier framework drafts framed §9 as "the openOODA substrate each product consumes." That framing is now retired. The five services do not depend on any external substrate — they are self-contained Rust binaries. §9 is preserved at its original number for cross-reference stability with §§1–18, but its content is now: "there is no substrate; here is what 'no substrate' means in practice."

### What "no substrate" means in practice

| Concern | Where it lives |
|---|---|
| **Process / IO / filesystem** | `std::process`, `std::fs`, `std::io` |
| **Concurrency** | `std::thread`, `std::sync`, `std::mpsc` (no `tokio`, no `async-std`) |
| **Crypto (when needed)** | Vendored single-file C, OR a hand-rolled implementation in pure Rust against `std::*` — *not* a crates.io crate, *not* an `extern "C"` to a non-std library |
| **JSON / TOML / policy parsing** | Hand-rolled parsers for the subset we use, in pure Rust |
| **Argument parsing** | `std::env::args` + a `match`, hand-rolled |
| **Errors** | `enum AppError { ... }` with `impl Display + Debug` and `?` propagation |
| **Logging** | `println!` for machine-readable output; `eprintln!` for diagnostics |

### What this section is NOT

- It is **not** a license to call into any external library at all. If a service ever needs to reach outside `std::`, it must be vendored (a single-file `vendor/<name>.rs` with a header citing origin and license), per §12.
- It is **not** a way to re-introduce crates.io dependencies "because the section is short." §12 still applies.
- It is **not** an argument that the five services are identical. They share a *style* (Rust + std::, ≤ 256 LoC per file) but each binary is its own crate with its own `Cargo.toml`, its own `src/`, its own test target.

### How this connects to §1, §12, §15

- **§1** says Rust only. §9 makes that concrete: the Rust compiler + std:: is the entire toolchain, with the user's standing rule that the toolchain is "latest stable" — no pinning.
- **§12** says no Cargo dependencies. §9 is the architectural expression of §12: there is no substrate to depend on, because the substrate is `std::`.
- **§15** (threat model) is unaffected by §9 in the obvious direction — Vigil's "registry / lockfile attacker" is a registry-layer threat, not a std-layer threat. But §15 *is* affected in the non-obvious direction: because there is no substrate, Vigil's "out of scope" list is shorter than it would otherwise be — there is no registry to compromise, no third-party lockfile authority to subvert.

### One-line policy

> If a junior Rust engineer with the Rust Book open can't read a module top-to-bottom and tell you what each line does, the module is doing too much.
