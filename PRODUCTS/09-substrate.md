# Section 9 · Cross-cutting — the openOODA substrate each product consumes



This is what makes the five products a family, not five disjoint tools:

| Substrate primitive | Lives in | What each service consumes |
|---|---|---|
| **Seance / Necrometer fate table** | openOODA/seance, shipped as the `seance` crate | Vigil's `probe.rs` (canonical dormancy). Boneyard's `enrich.rs` (canonical dormancy axis). |
| **bb flight recorder** | openOODA/bb | All five audit their external actions into a `.blackbox/flight.json`, signed by the next item. |
| **ML-DSA-65 signing** | `oodar/sec/pqc/mldsa/` | Every audit ledger row is signed. Proven and Aegis sign artifacts. Vigil signs the scan row. |
| **opm cap-aware catalog** | openOODA/opm | When a binary is distributed via `opm install vigil`, the install event becomes a signed provenance. Used for the 26-cap-token distribution surface. |
| **Merkle AST** | oodac (compiler) | Proven uses it for source↔binary diff. Aegis uses it for the migration plan's diff-to-target. |

A service that doesn't consume at least **one** of these is not part of the family. A service that consumes **four of five** is the deepest substrate demo — those are the ones posted on Hacker News.
