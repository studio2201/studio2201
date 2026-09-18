# Section 3 · Zero trust — within the service and across the boundary



**No implicit state.** Every module takes its dependencies as constructor arguments or function parameters. No `lazy_static!`, no `OnceLock<Mutex<State>>`, no module-level `mut` variables.

**No inheritance trust.** A submodule of `report.rs` does not trust the output of a submodule of `probe.rs` — both are validated at their respective boundary, then composed.

**External input is hostile until validated.** Manifests come from real-world npm registries full of malformed JSON. Treat them as bytes first, validate shape, then map. Never trust a schema you didn't author.

**Audit every external action.** Tool calls, network requests, file reads, file writes — each one produces a row in a flight ledger, signed with ML-DSA-65 from `oodar/sec/pqc/mldsa/`. The signature chain is what makes a buyer trust the output in court.

**Cap-aware means capability-scoped.** Even internal-to-this-binary "calls" (e.g., manifest parser calling into scorer) are typed as functions with explicit input boundaries. Borrow from `oodar/sec/cap/caps.h` semantics: every capability is a token, attenuation is HMAC-bound, fail-closed is the default.
