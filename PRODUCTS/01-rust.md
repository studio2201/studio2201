# Section 1 · Rust, idiomatic, lean



- **Edition 2021**, MSRV stable.
- `publish = false` in `Cargo.toml`. These are local-first services, not crates.io packages.
- `profile.release`: `lto = "thin"`, `codegen-units = 1`, `strip = true`, `panic = "abort"`. Result: a single ~1 MB binary.
- **Zero direct deps in `Cargo.toml`.** Every line of Rust comes from `std::` or a vendored source. See §12 ("From scratch"). This *replaces* the older ≤ 10-draft ceiling.
- **No async by default.** These are CLI tools, not servers. Sync reads & writes are correct.
- `Result<T, E>` everywhere. No `unwrap()`, no `expect()`, no `panic!()` outside of test code.
- **No `unsafe`** in any of the five. If you think you need it, propose an `unsafe` budget for review; if you don't have one yet, don't write it.
