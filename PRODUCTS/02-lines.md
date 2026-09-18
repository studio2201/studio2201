# Section 2 · ≤ 256 lines per `.rs` file (the hard ceiling)



This is the studio2201 doctrine's signature constraint. Apply it without negotiation.

**Why:** a file you can read in one screen is a file you can audit, review, hand to a new contributor, and replace. The cost of a long file is always paid later by someone you don't know yet.

**How to enforce it:**

- Each `.rs` file has **one concept** at its center. The file's `pub` surface is that concept, exposed.
- If a file hits ~200 LoC, split it now, not after the next feature.
- Names are verbs (functions) and singular nouns (structs); the file name is the noun, e.g. `report.rs` exports `render_markdown()`.

**No-go file names**: `utils.rs`, `helpers.rs`, `common.rs`, `misc.rs`. If a thing is hard to name, it doesn't belong in one file.
