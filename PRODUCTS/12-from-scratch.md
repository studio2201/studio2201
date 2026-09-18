# Section 12 · From scratch — every line of Rust comes from `std::` or vendored code



The principle: an AI agent (or a new Rust engineer) reading the Rust Book should be able to rewrite any module in the five services from spec alone. No Cargo dependencies. No mystery APIs to look up.

### Rule

`Cargo.toml` has no `[dependencies]` section. Each service's binary builds with `cargo build --release --offline` and produces a working tool without reaching crates.io.

### What is allowed

| Source | Acceptable |
|---|---|
| `std::*` — anything in Rust's standard library | Yes, always. |
| `vendor/<name>.rs` — single-file public-domain implementations inlined at build time | Yes, when the spec is small and stable. JSON parser, regex VM, TOML parser for the specific dialect we use. Each gets a header comment citing origin and license. |
| `crates.io` dependencies | **No.** |
| Any other external library (e.g. an `extern "C"` against an out-of-tree `.so` or `.a`) | **No.** |

### What this means in practice

- **Argument parsing** becomes ~80–120 lines of `std::env::args` + `match`, not `clap`.
- **JSON I/O** becomes a hand-rolled `Reader` + `Writer` for the shapes we use (object, string, number, array, bool, null) — ~300 LoC per service that needs it. JSON for `package.json` and `Cargo.lock` is a small subset of the spec.
- **TOML** for the policy files (Vigil, Snip, Boneyard) becomes a 100–150 LoC hand parser for the schema we control. Do not chase full TOML 1.1 compliance; chase the subset the policy files use.
- **Errors** become `enum AppError { ... }` with `impl Display + Debug`, propagated via `?` and converted via `impl From<...> for AppError`. No `anyhow::Error`.
- **Logging** uses `std::io::_stderr().lock().write_fmt(fmt::format(...))` or `println!` for machine-readable output. Optional: a 50-line `tracing`-style macro you write yourself.
- **Diff / regex / grep** uses a hand-written pattern matcher for the specific shapes we care about (RSA signatures, hardcoded API keys, missing CSP headers). ~150 LoC. Keep the shapes fixed; the matcher is a one-shot.

### Cost (honest)

- ~30–40% more LoC per service.
- Some UX polish lost (well-formatted `--help` auto-generated from a struct; color output; structured error chains that ship with `anyhow`).
- The first version of each service is larger than the equivalent `cargo add clap && 30 LoC of struct definition`. You'll write more code upfront.
- A small amount of bug surface (your JSON parser vs. `serde_json` for the wild). Mitigated by deterministic example-driven integration tests in `tests/fixtures/`.
- We can never point to "use the well-tested crate" as an excuse. That excuse was never great. We'd rather be honest about what we wrote.

### Benefit (honest)

- **AI-rebuildable.** A model can rewrite any module in minutes because there is no API surface to learn. The whole point: the code *is* the spec.
- **Zero supply-chain risk at the dependency layer.** No upstream crate can break, get poisoned, or change license. PQC-SLSA-grade by construction.
- **Fully reproducible from source.** `cargo build --release --offline` works on a fresh machine. No network needed.
- **Honest about what we wrote.** Every line is ours, with our name on it. The CVE is our CVE; the bug is our bug; the fix is our fix.

### How to enforce

- A `scripts/no-deps.sh` (or `make check-deps`) that greps the relevant `Cargo.toml` files for `[dependencies]` and exits non-zero if found. Run it in CI.
- A code-review rule: "if you find yourself adding a dep, you have to add a comment explaining why std fails." Once a year you admit a dep. Until then: don't.
- The exception, if ever needed, is `cargo` itself and the `rustc` that ships with `rustup`. Neither counts as a service-side dep.

### One-line policy summary

> If a junior Rust engineer with the Rust Book open can't read a module top-to-bottom and tell you what each line does, the module is doing too much.

That's §12. It's the spine of the framework. Without it, everything else in `PRODUCTS.md` is decoration.

---

**The first sentence of every product README must say, in plain English:**

> *"This product answers one question: [§0 question]. It is written in Rust against `std::` only. Status: [pre-release scaffold | v0.x.0 | 1.0.0]. License: Apache-2.0. Deps: zero crates.io."*

If a README can't lead with that sentence, the service isn't ready to be a product.
