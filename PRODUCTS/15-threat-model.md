# Section 15 · Threat model — name the adversary before you write the tool


**Threat model first, code second.** A CISO reading any of the five services should be able to read one short document — `docs/threat-model.md` — and walk away knowing exactly which adversary the tool defends against, where the trust boundary sits, what it explicitly does *not* defend, and what residual risk they are accepting by adopting it. A threat model that says "various threats" is not a threat model; it's a placeholder for thought.

### Rule

Every service ships a `docs/threat-model.md` at its repo root. The doc has exactly four sections, each named below, and names a **specific adversary** — not a category. The five adversaries are pre-decided once in this section and reused per service; each per-service doc is a 5-line application of the template to its specific tool.

### The template (used by all five services)

```markdown
# Threat model — <service>

## 1. Adversary
<who specifically is the threat, named in one sentence>

## 2. Trust boundaries
<what we trust; what we don't; what crosses the boundary>

## 3. Out of scope
<what we explicitly do not defend against>

## 4. Residual risk
<known weaknesses a buyer should weigh before adopting>
```

A threat model missing any of the four sections fails review. A threat model that fills a section with "various threats" or "potential attackers" fails review. Specificity is the contract.

### The five pre-named adversaries

| Service | Adversary (named in `docs/threat-model.md §1`) |
|---|---|
| **Vigil** | A malicious or unmaintained npm / cargo / pip registry, plus an attacker who controls the user's lockfile at install time. |
| **Proven** | An attacker who can poison the build environment between source commit and the published binary artifact (i.e., the source is honest but the bytes are not). |
| **Aegis** | A quantum-capable nation-state actor performing "harvest now, decrypt later" against RSA-encrypted traffic originating from a Federal contractor that must migrate to PQC by 2030. |
| **Snip** | An attacker who can craft an LLM-generated diff that bypasses static rules (regex, secret-scan) while still exfiltrating data via a novel encoding. |
| **Boneyard** | A platform-eng lead whose org has had a dormant-but-public-facing repo compromised through an unmaintained dependency. |

These are pre-decided so the threat models do not become performative. The five services' docs are 5-line instances of this table, not creative writing.

### Contract clauses

The threat model is a contract. The contract is enforced by §13 (functional QA) and §14 (functional edge cases).

| Clause | What it requires |
|---|---|
| **C1** | A `docs/threat-model.md` exists at the repo root of every service. |
| **C2** | Section 1 names a **specific** adversary (one of the five above), not a category like "threat actors." |
| **C3** | Section 3 ("Out of scope") is **non-empty**. An empty out-of-scope section is a contract violation; the model is claiming to defend everything, which is dishonest. |
| **C4** | Section 4 ("Residual risk") names at least one weakness a buyer should weigh. The weakness must be concrete (e.g., "registry TLS-pinning is not enforced by Vigil v0.1.0"), not abstract ("may have bugs"). |
| **C5** | The threat model is **referenced from `README.md`** — a buyer who reads the README sees the link within three clicks of the project description. |
| **C6** | The threat model is **referenced from `SECURITY.md`** (§17) — disclosure scope is bounded by what §15 declares in scope. |
| **C7** | When a service's scope changes (new verb, new input), the threat model is updated in the **same commit** as the scope change. A scope change without a threat-model update fails review. |

### Worked example — Vigil's threat model, verbatim

```markdown
# Threat model — vigil

## 1. Adversary
A malicious or unmaintained npm / cargo / pip registry, plus an attacker
who controls the user's lockfile at install time. The attacker can ship
a "looks-fine" package that is a backdoor, or rewrite a pinned version to
one with a known CVE.

## 2. Trust boundaries
We trust: the user's policy file (`vigil.toml`), the user's git history
of the lockfile, and the SHA-256 of the lockfile at the time of scan.
We do not trust: the registry, the contents of any package fetched at
scan time, or the network between the user's machine and the registry.

## 3. Out of scope
Vigil does not defend against: compromised developer credentials, malicious
post-install scripts that run after Vigil has approved a package, and
supply-chain attacks that exploit build-time tools the user has installed
on their own machine (e.g., a poisoned `npm` binary).

## 4. Residual risk
Vigil's classifier relies on heuristics (last-commit date, downloads,
CVE cross-reference). A targeted attack that *looks* active for the 30-day
heuristic window will pass. The buyer is accepting "best-effort flagging,
not a guarantee."
```

A CISO reads this in 60 seconds and walks away knowing what Vigil does and does not promise. That is the bar for the other four threat models.

### Connection to §3 (Zero Trust), §12 (From Scratch), §13 (QA), §14 (Edge Cases)

- **§3 (zero trust).** §15 is the operating expression of §3. §3 says "verify everything"; §15 says "and here is exactly what we verify, and here is exactly what we don't."
- **§12 (zero deps).** Threat models for Vigil, Proven, and Boneyard list "registry compromise" or "lockfile tampering" — exactly the attack surface that §12's "no crates.io dependencies" stance was designed to shrink. Aegis and Snip are about encoding and detection, not registries; the cross-reference is "the threat model is what §12 buys you."
- **§13 (functional QA).** Every "out of scope" line in a threat model is a testable claim that the service does *not* do the disallowed thing. §13's QA matrix includes at least one adversarial test per "out of scope" clause, asserting the service declines to perform the out-of-scope operation.
- **§14 (edge cases).** The residual-risk section is full of boundary cases ("the 30-day window") that §14's edge-case tests verify with concrete input. A residual-risk claim without a §14 test is unverified.

### How to audit §15 across all five services

A 3-line check, runnable as `bash scripts/audit-threat-models.sh` (or as a one-shot from the doctrine root):

```bash
# A15-A: every service has a docs/threat-model.md
for s in vigil proven aegis snip boneyard; do
  test -f studio2201/$s/docs/threat-model.md || echo "MISSING: $s"
done

# A15-B: each threat model names its specific adversary (string match)
for s in vigil proven aegis snip boneyard; do
  grep -q "## 1\. Adversary" studio2201/$s/docs/threat-model.md \
    || echo "MISSING §1: $s"
done

# A15-C: each threat model has a non-empty "Out of scope" section
for s in vigil proven aegis snip boneyard; do
  awk '/## 3\. Out of scope/,/## 4\./' studio2201/$s/docs/threat-model.md \
    | grep -q '.' || echo "EMPTY §3: $s"
done
```

A failure in any of the three lines is a §15 contract violation and a blocker for release.

### One-line policy

> A threat model without a named adversary is a wish, not a contract.
