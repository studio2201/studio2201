# Section 8 · Human-understandable file and folder names



**Folder layout** (each service):

```
service/
├── Cargo.toml
├── Cargo.lock
├── LICENSE                     (Apache-2.0)
├── README.md                   (status, install, demo, license)
├── CHANGES.md                  (Keep a Changelog, semver)
├── .gitignore                  (target/, generated reports, secrets)
├── src/
│   ├── main.rs                 (CLI; thin)
│   ├── lib.rs                  (canonical re-exports)
│   └── <one-concept>.rs        (per §7)
├── tests/
│   ├── integration.rs          (full scenarios)
│   └── fixtures/               (sample inputs)
├── examples/
│   └── demo.sh                 (end-to-end against fixtures)
├── policies/                   (TOML, where the service has gates)
└── docs/
    └── architecture.md         (data flow diagram in plain text)
```

**Naming rules:**

- File names are lowercase, snake_case, singular noun per the convention. `manifest.rs` not `manifest-parser.rs`. The file IS the parser; the name describes the noun.
- Folder names are singular where possible: `tests/`, `docs/`, `fixtures/`. Plural only for plurals (`fixtures/`, `policies/`).
- Module names match filenames: `pub mod manifest;` in `lib.rs` corresponds to `src/manifest.rs`.
- Public API surface in `lib.rs` is small: `pub use scoring::{AxisKind, Score};` — re-export only the canonical surface, not internal-detail dumps.
