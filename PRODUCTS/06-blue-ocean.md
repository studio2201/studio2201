# Section 6 · Blue ocean positioning — name the gap each service fills



| Product | Adjacent big-tool | What they own | What we own (the gap) |
|---|---|---|---|
| **Vigil** | Socket, Snyk (security-flavored dependency graph) | Block malicious deps, surface advisories | "How dead are my deps?" — dormancy as a first-class axis |
| **Proven** | Sigstore, Sigsum, in-toto | Classical signature SLSA L3 | "PQC-signed SLSA L3+ that survives past 2030" |
| **Aegis** | Cloudflare/Zscaler (PQC at the edge), AWS KMS (PQC primitives) | PQC transport | "Show me every RSA call in my source" + one-command replacement |
| **Snip** | Snyk (general SAST), Aikido, Semgrep | Committed-code static analysis | "AI-generated diffs specifically" |
| **Boneyard** | GitHub code-search, internal "what is dead?" reports | Repo search, no aggregate dormancy signal | "Org-wide dormancy with a CI gate" |

The rule: **if a Fortune 500 CISO reads the README and instantly knows which big tool it's adjacent to, you've done your job.** Then the README pivots: "this is the gap that big tool doesn't fill — this is why you'll switch to us for that slice."
