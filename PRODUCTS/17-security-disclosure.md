# Section 17 · Security disclosure — one tab, one window, one rule


**Security disclosure is a GitHub-recognized channel, not a PGP key.** Each service ships a `SECURITY.md` (≤ 30 LoC) that points reporters at the GitHub Security Advisories tab for that repo. There is no `security@studio2201.example` mailbox, no PGP key infrastructure, no Signal number to maintain. The trade-off is acknowledged: GHSA is GitHub-only — but all five repos already live on GitHub. The benefit is that reporters don't have to learn our protocol; the protocol is GitHub's.

### Rule

Every service ships `SECURITY.md` at the repo root. The file is in the standard GitHub-recognized format. Disclosure goes through GitHub Security Advisories (GHSA); the response window is documented in the file. Pre-1.0.0 services use `Severity: Low/Medium/High` instead of CVSS; ≥ 1.0.0 services issue CVEs via MITRE for any issue scoring ≥ 4.0 on the CVSS v3.1 base score.

### The template (used by all five services)

```markdown
# Security Policy

## Supported Versions
| Version | Supported          |
|---------|--------------------|
| 0.x.y   | :white_check_mark: |
| < 0.1   | :x:                |

## Reporting a Vulnerability
- Open a private security advisory at
  https://github.com/studio2201/<service>/security/advisories/new
- Response window: 14 days for triage, 90 days for coordinated disclosure
- We credit the reporter in the fix commit unless they request otherwise
- Severity is assessed via CVSS v3.1 base score; CVEs are issued via
  MITRE if score ≥ 4.0 (post-1.0.0 only)
- Pre-1.0.0 advisories use GHSA only (Severity: Low / Medium / High);
  CVEs are reserved for stable releases
```

The template is intentionally minimal. Reporters do not need to read 50 lines of policy before clicking "Report a vulnerability."

### Contract clauses

| Clause | What it requires |
|---|---|
| **C1** | `SECURITY.md` exists at the repo root of every service and cites the GitHub Security Advisories URL for that repo. |
| **C2** | The response window is **explicit and bounded**: ≤ 14 days for triage, ≤ 90 days for coordinated disclosure. An open-ended "we'll respond when we can" fails the clause. |
| **C3** | Bugs that are **not** security issues are tracked in GitHub Issues, not in `SECURITY.md`. `SECURITY.md` is exclusively for security disclosures; mixing bug reports in pollutes the disclosure channel. |
| **C4** | A reported issue becomes a GHSA **at the moment it is reported as a security issue**, not after triage. Pre-1.0.0 services use `Severity: Low/Medium/High` instead of CVSS scoring. |
| **C5** | **Public disclosure before a fix ships requires the maintainer's explicit approval.** No advisory is published automatically by tooling. The maintainer reviews and clicks "Publish" — no automation shortcut. |
| **C6** | `SECURITY.md` references the service's `docs/threat-model.md` (from §15) so the reporter knows the disclosed issue's scope. A disclosure outside the threat model's stated "Out of scope" is acknowledged as such in the GHSA, not silently accepted. |
| **C7** | After a fix ships, the GHSA is closed with a `fixed in: <version>` note and the `CHANGES.md` (per service) gets a `### Security` entry citing the GHSA ID. |

### Severity policy, pre-1.0.0 vs. post-1.0.0

The framework recognizes that pre-1.0.0 services are not production-grade by §10's definition. So:

| Service maturity | Severity ladder | Public identifier |
|---|---|---|
| **Pre-1.0.0** (any 0.x.y) | `Severity: Low` / `Severity: Medium` / `Severity: High` | GHSA ID only |
| **Post-1.0.0** (1.0.0 and later) | CVSS v3.1 base score, ≥ 4.0 → CVE | GHSA ID + CVE for CVSS ≥ 4.0 |

A `Severity: High` issue in a pre-1.0.0 service still gets fixed promptly and credited — it just doesn't get a CVE number. The transition from "GHSA-only" to "GHSA + CVE" is itself a release-event: when a service ships 1.0.0, the first security advisory at the new severity level documents the policy change in its body.

### Worked example — disclosure timeline for a hypothetical issue

Imagine a contributor reports a path-traversal bug in Vigil v0.1.x on day 0. The flow:

| Day | Action |
|---|---|
| **Day 0** | Reporter opens a GHSA via the `SECURITY.md` link. The advisory is private. GHSA ID assigned automatically. |
| **Day 1–3** | Maintainer triages. Severity is assessed: this is `Severity: High` (pre-1.0.0; no CVSS number issued). |
| **Day 3–7** | Fix is written. A `#[test]` in `qa/edges/` covers the boundary (per §14). The fix is reviewed. |
| **Day 7** | Fix commit lands on `main`. Commit message cites the GHSA ID. `CHANGES.md` is updated with `### Security` entry. |
| **Day 7–14** | Fix is built into a `vigil v0.1.1` release. The release is signed (per §16's reproducibility contract). |
| **Day 14** | GHSA is published. The disclosure cites: the GHSA ID, the fix commit, the v0.1.1 release, the credited reporter (per their preference), and a link to the `docs/threat-model.md` section that explains why this issue was *in* scope. |
| **Day 14+** | Public. `CHANGES.md` already has the entry; readers of the changelog before the GHSA publication can see that a fix shipped. |

The 90-day coordinated-disclosure window is the upper bound. Most issues close in 14 days. The window is the policy, not the target.

### What `SECURITY.md` is NOT

- It is not a bug tracker. Bug reports go to GitHub Issues.
- It is not a feature-request channel. Feature requests go to GitHub Issues.
- It is not a support forum. "How do I…" goes to GitHub Discussions or the issue tracker.
- It is not an email inbox. We do not run a security mailbox. All communication happens via GHSA comments, which are GitHub-mediated and audit-trailed.

This separation is deliberate. Mixing channels means a security-relevant issue sits in the bug queue, never gets a GHSA, and ends up shipped without coordination. §17 is the discipline that prevents that.

### Connection to §3 (Zero Trust), §12 (From Scratch), §13 (QA), §14 (Edge Cases)

- **§3 (zero trust).** §3 says "verify everything, including the verifier." §17 is the verifier-of-verifier: the disclosure channel is itself subject to audit (who opened the GHSA, who published, when). GitHub provides the audit trail.
- **§12 (zero deps).** §17 uses **no PGP infrastructure** because PGP keys are themselves a small dependency layer that requires rotation, revocation, and verification tooling. GHSA removes all of that. Consistent with §12's "fewer moving parts in the supply chain."
- **§13 (functional QA).** §13 says "every function has a test." §17 says "every disclosed vulnerability has a regression test that prevents re-introduction." The `#[test]` written for the fix is the closure of the disclosure loop.
- **§14 (edge cases).** Path-traversal bugs, off-by-one errors in input validation, integer overflow in size calculations — these are exactly the edge cases §14 covers in advance. A disclosure that lands on a §14-style boundary bug means §14 wasn't thorough enough for that boundary; the disclosure drives the next §14 addition.

### How to audit §17

```bash
# A17-A: every service has SECURITY.md with the GitHub-recognized section
for s in vigil proven aegis snip boneyard; do
  grep -q "## Supported Versions" studio2201/$s/SECURITY.md \
    || echo "MISSING: studio2201/$s/SECURITY.md"
done

# A17-B: every SECURITY.md cites the GHSA URL for that repo
for s in vigil proven aegis snip boneyard; do
  grep -q "github.com/studio2201/$s/security/advisories/new" studio2201/$s/SECURITY.md \
    || echo "WRONG URL: studio2201/$s/SECURITY.md"
done

# A17-C: no service exposes a PGP key fingerprint (we don't run one)
for s in vigil proven aegis snip boneyard; do
  grep -q "PGP" studio2201/$s/SECURITY.md && echo "PGP FOUND: $s"
done
```

A failure in any of the three lines is a §17 contract violation.

### One-line policy

> The disclosure channel is the same channel as the code — GitHub. Anything else is a place for issues to get lost.
