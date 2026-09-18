# Section 5 · Systems thinking — every service is a feedback loop



Each of the five produces output that modifies state and feeds back:

```
   scan → report → fix → re-scan → new state
```

Concretely:

- **Vigil** is `vigil scan` → `SUPPLY-CHAIN.md` → user swaps a dep → `vigil scan` again. Different output, same loop.
- **Proven** is `proven sign` → attestation ledger → buyer verifies. Closed loop.
- **Aegis** is `aegis scan` → migration plan → `aegis migrate` → CI gate blocks regressions. Closed loop.
- **Snip** is `snip check` → Ship/Fix/Block verdict → PR comment → next PR. Closed loop.
- **Boneyard** is `enrich` → `report` → `policy check` (gate). Closed loop.

If a service has no closed loop — i.e., the user sees output and goes away — it's a one-shot and not yet a product. The `<verb>` of every service is a step in a loop.
