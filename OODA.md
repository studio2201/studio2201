# OODA (Any Repo)

**Location:** `~/OODA.md`  
**Scope:** Any codebase in studio2201.  
**Purpose:** How to work on tasks: **Observe → Decide → Act → Lock → Ship**.

**Always load:** `~/RULES.md`.  
**When you need to test security deeply:** `~/PROBE.md`.  
**Product goals:** `DESIGN.md` in the project root.

---

## Always on (from RULES)

- Find the real cause. Plan just enough to start working.
- Do work every turn. Do not only look.
- Tell the truth. If work fails, fail safely.
- Keep things locked by default.
- Keep files under 256 lines. Prove your work with tests.
- Choose at most 5 tasks at a time.

Full rules: **`~/RULES.md`**.

---

## Setup (fill every session)

```text
ROOT:
How to build & run:
Backlog file (or issues):
Handoff file (e.g. PROGRESS.md):
Must-not-break rules (check tests/ in ROOT):
Cleanup commands (fmt, lint):
Is ROOT/DESIGN.md present?:
```

---

## 1. Observe

1. Read the handoff notes and backlog.
2. Check the code health. Are there files over 256 lines? Are tests failing?
3. Look for security risks (passwords, tokens, file writing, network calls).

Ask: What is the most important thing to fix or build next?
Plan only enough to start writing code.

---

## 2. Decide (Choose at most 5)

Choose tasks in this order:

1. **Fix broken code or tests.**
2. **Fix security holes.**
3. **Build the next planned feature.**
4. **Plan missing features** (add to backlog, then do them).
5. **Clean up messy code** (split large files).

---

## 3. Act

- Write real code in the project.
- If you cannot finish, leave the code safe and tell the user.
- Split files if they get close to 256 lines.
- Write tests that prove your code works.
- Keep things locked. Do not put secret keys in the code.

---

## 4. Lock (Check before shipping)

You must check these things before you ship:

1. **Check rules:** Run `RULES.md`. Are files under 256 lines? Did you delete old debug code? Are there any secret keys?
2. **Check tests:** Run `cargo test` (or the project's tests). Everything must pass.
3. **Check backlog:** Only check off tasks if the code is fully done and tested.

If any check fails, do not ship. Fix the code first.

---

## 5. Ship

- Commit and push your code to the server. Do not force-push to main.
- Write a short report to the user: what you built, what rules you followed, and what to do next.
- The code must be cleaner than when you started.

---

## When to run PROBE

Use **`~/PROBE.md`** when:

- You need to test security rules deeply.
- You think the code is hiding errors or passing tests by cheating.
- You just made a big change to security, passwords, or networking.

PROBE adds extra security checks. It does not replace the normal checks in step 4.

---

## Master prompt

```text
Run OODA from ~/OODA.md with ~/RULES.md loaded.
Target ONE repository ROOT. Fill the setup.
ROOT/DESIGN.md is for project goals.
Observe → Decide (≤5) → Act → Lock (check tests) → Ship.
First principles, do real work, fail safely, keep things clean, keep things locked.
Do not cheat on tests. Do not guess how the project works.
For deep security tests: ~/PROBE.md.
```
