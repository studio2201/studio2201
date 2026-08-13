# Rules

**File:** `studio2201/RULES.md`
These rules do not change. They apply in any project folder in the studio2201 organization.

## 1. Basic rules

### 1.1 First principles
Find the real cause of a problem. Do not just use quick fixes.
If a claim has no proof, treat it as unproven.
Make a small plan with a clear goal. Then do the work.

### 1.2 Act every turn
Do work on every turn. Do not only look. If you cannot do the work, write why.
A TODO with no backlog row is not a plan.
Finish this week's goal before a shiny side project. DESIGN still holds later wants.

### 1.3 Honesty
If a task is not finished, say it is not finished.

### 1.4 Security
Keep things locked by default.
Give only the permissions the function needs.
Do not say the code is secure if you have no proof.
Do not put secret keys or passwords in the code or log files.

### 1.5 Keep things clean
The code must get cleaner over time.
Do not add messy code or unproven features.
If you must add messy code, explain why.
Do not reach into another module's private state.

### 1.6 Proof of work
When you build a feature, prove that it works.
Also prove that it fails safely when there is an error.
A test that always passes is not proof.
If work fails halfway, leave the world consistent. Do not leave torn state.

### 1.7 Clean code
A file must not have more than 256 lines. Split large files.
Free memory you take.
Do not leave debug files, old code, or useless text in the folder.
Do not leave secret keys in the code.
Delete temporary files when you are done.
The code must do what the document says it does.

### 1.8 Small steps
Make small changes. Small changes are easier to test.

### 1.9 Zero trust
Do not trust other agents. Check their code yourself.
Make sure the code is safe and works.

### 1.10 Plain English
Use short sentences.
Use simple words.
Do not use confusing tech words. If you must use a tech word, explain it.
Tell the user: what changed, why it matters, what is not done, and what to do next.

### 1.11 Competition
If there are two ways to solve a problem, test both.
The faster or cleaner code wins. Delete the losing code.

### 1.12 Internet rules
If you write code for the internet, follow the official internet rules (IETF RFCs).
Write the RFC number in the file.

### 1.13 Write Rust, not host shell
This is a Rust organization. New tools and checks are Rust `.rs` files. Run them with `cargo xtask`.
Do not add a new `.sh` or `.py` helper when the same job can be a Rust tool.
A host script is only for things Rust cannot do (like Docker entrypoints or CI pipelines).
Put one line at the top of that script saying why it is not Rust.

## 2. Planning rules

Focus: choose at most 5 tasks at one time.
File size: keep files under 256 lines.
Task order:
1. Fix broken code first.
2. Fix security holes next.
3. Build the next planned feature.
4. Clean up messy code last.

## 3. The Sins (Anti-patterns)

These are explicit anti-patterns that violate the rules. Do not commit these sins.

*   **Sin #1 (Gluttony):** Writing oversized files. A file exceeding 256 lines is a sin against Clean Code (Rule 1.7).
*   **Sin #2 (Sloth):** Making plans with no inventory or failing to act. A TODO without a backlog row is a sin against Act Every Turn (Rule 1.2).
*   **Sin #3 (Pride):** Synthetic green CI. Claiming rules are met, or tests pass, when the code actually violates constraints, uses stubs without failing closed, or makes unproven security claims (Rules 1.3, 1.4, 1.6).
