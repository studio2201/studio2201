# Swarm Operations

**Location:** `~/SWARM.md`  
**Scope:** How multiple agents work together.  
**Purpose:** Do not waste AI tokens. Keep costs low.

---

## 1. Pick the right model

Do not use heavy, slow models for easy tasks.

| Model | When to use |
|------|-------------------|
| **Fast / Light** | Fixing grammar, running simple shell scripts, checking file limits (`< 256` lines). |
| **Heavy / Pro** | Deep thinking, writing complex code, fixing hard bugs across multiple files. |

---

## 2. Small steps, fail fast

Do not give agents huge jobs like "Build a full backend". Huge jobs waste tokens and cause the AI to forget things.

- **Small jobs:** Give agents small, clear tasks. One file at a time.
- **Fail fast:** Tell the agent to stop and report back immediately if it hits a problem. Do not let it guess in endless loops.

---

## 3. Do not spawn debug agents

If an agent gets stuck in a loop or breaks a build:
- **DO NOT** spawn a new, heavy agent to debug the first agent. This wastes tokens.
- **DO** use fast tools like `grep_search`, `list_dir`, or `view_file` to find the problem yourself.
- **DO** kill stuck background tasks to save tokens and money.

---

## 4. Keep agents separated

If you spawn multiple agents at the same time, make sure they edit different files.
- If two agents try to edit the exact same file, they will break it.
- Keep them in separate branches or folders if they must work on the same system.

---

## 5. Security agents (QA Probes)

- Security agents should only look for edge cases (huge files, broken networking, fake passwords).
- If a security agent finds a hole, it should put a hard stop in the code (like an exit or a panic). It should not try to rewrite the whole feature.
