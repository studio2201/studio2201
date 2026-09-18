# Code Probe (Any Repo)

**Location:** `~/PROBE.md`  
**Scope:** Any codebase in studio2201.  
**Purpose:** Deep security testing and finding broken assumptions. This is not a simple checklist. This is a hostile test.

**Always load:** `~/RULES.md`.  
**For normal work:** `~/OODA.md`.  

**Rule check:** You must check rules (like the 256-line limit) every time you ship (`OODA.md`). PROBE adds extra, deeper tests. It does not replace the normal checks.

---

## 1. First principle

**Software only works until an assumption breaks.**

PROBE does not "verify the code works." PROBE destroys assumptions behind the code, the docs, and the tests. PROBE looks for code that fails open, broken states, fake tests, and security holes.

---

## 2. Default Rules

| Rule | Default | Notes |
|----------|---------|--------|
| **File Size Limit** | **256 lines** | Split files if they are larger. |
| **Hang test** | 5–10s max | If the code hangs, it fails the test. |
| **Verify bugs** | 2 times | Run the test twice to be sure a bug is real. |

---

## 3. How to break the code (Test Packs)

Start at the edge of the system (network, files, login). Work backward.

### Boundary tests
- Run the code exactly right with full permissions. (Must pass)
- Run the code with missing permissions or no login. (Must fail safely)
- Run the code with a fake or wrong token. (Must block)
- Make the OS fail (full disk, missing file). (Must return an error, not silently pass)

### Chaos tests
- Give the code too little memory or too few file descriptors. (Must not hang)
- Try to write to a read-only file. (Must fail safely)
- Send valid data and garbage data at the same time. (Must not lock up)
- Send empty, huge, or broken files. (Must fail safely)

### Lifecycle tests
- Files and network connections must be closed when done.
- Temporary files must be deleted, even if the code crashes.
- Passwords and secret keys must not be printed to log files.

---

## 4. Check the Rules (The 7 Sins)

Check the rules from `RULES.md`. Look for the 7 Sins:
1. Are there oversized files (Gluttony)?
2. Are there plans with no action (Sloth)?
3. Are tests cheating to look green (Pride)?
4. Does code reach into private states (Envy)?
5. Are we chasing shiny features before fixing bugs (Lust)?
6. Does the code leak memory or ask for too many permissions (Greed)?
7. Is the world left in a broken state after an error (Wrath)?

---

## 5. Report Findings

When you find a bug, write a report using this format:

```markdown
## Finding F##
Severity: CRIT | HIGH | MED | LOW
What broke:
How to trigger it (shell command):
What actually happened:
Why the normal tests missed it:
What to fix:
```

| Severity | Meaning |
|----------|---------|
| **CRIT** | Security bypass, or broken code acts like it worked. |
| **HIGH** | Code hangs, fails open, or does the wrong thing silently. |
| **MED** | Temporary files left behind, messy code, minor rule breaks. |
| **LOW** | Documentation is wrong, or file is close to 256 lines. |

---

## 6. Master prompt

```text
Run Code Probe from ~/PROBE.md with ~/RULES.md loaded.
Target ONE project.
Look for broken assumptions, fake tests, and security holes.
Do not trust the tests. Run the code yourself.
Check for the 7 Sins.
Write a report with the CRIT/HIGH/MED/LOW format.
```
