# Section 7 · Functional alignment and functional splits



Within any service, one concept per file. The file name is the noun; functions are the verbs.

| Service | File | Verb |
|---|---|---|
| **Vigil** | `manifest.rs` | parse & detect manifest flavor |
| | `probe.rs` | look up dep fates (pluggable backend) |
| | `score.rs` | composite risk per dep |
| | `report.rs` | emit `SUPPLY-CHAIN.md` + badge SVG |
| | `policy.rs` | gate CI against thresholds |
| **Proven** | `merkle.rs` | compute Merkle AST diff |
| | `sign.rs` | ML-DSA-65 sign the artifact + manifest |
| | `verify.rs` | re-derive and check |
| **Aegis** | `scan.rs` | find RSA/ECC/EVP_PKEY call sites |
| | `plan.rs` | render OMB M-26-15 migration plan |
| | `migrate.rs` | emit replacement primitives + EVP_PKEY shims |
| **Snip** | `audit.rs` | static pass over the diff |
| | `gate.rs` | Ship/Fix/Block verdict |
| | `report.rs` | one-pager in dialect (Cursor / Vercel / generic) |
| **Boneyard** | `enrich.rs` | five-axis scoring |
| | `report.rs` | Markdown org-wide report |
| | `policy.rs` | TOML gate |

If you need a new file, it needs a noun you can defend. "Utils" doesn't qualify.
