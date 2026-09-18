# Compatibility Matrix

Substrate dependencies across the five studio2201 services. The substrate
primitives (columns) are the openOODA packages each service links against
at build time. A service with `—` in a column does not link that primitive.

| Service       | seance | opm     | bb      | oodar-sec-pqc | oodar-sec-landlock | Last verified |
|---------------|--------|---------|---------|---------------|--------------------|---------------|
| vigil/0.1.x   | 0.4.x  | 0.1.x   | 0.1.x   | 1.0.x         | 4.5.x              | 2026-09-17    |
| proven/0.1.x  | —      | 0.1.x   | 0.1.x   | 1.0.x         | —                  | 2026-09-17    |
| aegis/0.1.x   | —      | 0.1.x   | —       | 1.0.x         | —                  | 2026-09-17    |
| snip/0.1.x    | —      | —       | 0.1.x   | 1.0.x         | —                  | 2026-09-17    |
| boneyard/0.1.x| 0.4.x  | 0.1.x   | 0.1.x   | —             | —                  | 2026-09-17    |

`—` = no dependency, per §9 Substrate map (the service does not use that
primitive). Each service's `Cargo.toml` is the authoritative source; this
matrix is the summary view.

`Last verified` is updated when the per-service repro baseline is rebuilt
(per §16) and the substrate versions in `Cargo.lock` are confirmed unchanged.
For pre-1.0.0 services this is a manual run, not CI.
