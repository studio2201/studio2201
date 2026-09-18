# Section 4 · First principles — ask before borrowing



For each service, the doc must answer one question before any code is written:

> *"If I forgot every existing tool, what question would I be answering, and what's the smallest answer that's also honest?"*

That answer becomes the README's first paragraph. If you can't write that paragraph, the code isn't ready.

Concrete examples already locked:

- **Vigil**: "are my dependencies maintained?" is the human question. The first-principles answer is the Necrometer/Seance fate table. Don't decorate it with CVEs, secrets, or AI-agent signals (those go in **Boneyard**, not Vigil).
- **Boneyard**: "is this repo safe to keep alive?" — answer is the dormancy index plus a small number of explicitly enumerated risk axes. The four non-dormancy axes in v0.1.0 are honest about being proxies (`source` field says so). v0.2.0 turns the proxies into real input pipelines.
- **Snip**: "is the code that was generated safe to ship?" — a static analyzer for AI-generated diffs. Don't conflate with general SAST; that's Snyk / Semgrep / Aikido. Snip owns the AI-generated diff slice.
