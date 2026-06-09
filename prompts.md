# Concise Prompts 📖

A compact collection of reusable prompts for code review, reverse engineering, implementation, refactoring, testing, and UI taste direction.

---

### Spam queue-able prompts

<details>
<summary><strong>2x review w/ subagents</strong></summary>

```md
When done, with fresh eyes,
review your changes with 2 subagents,
fix any issues,
then repeat until no issues are found.

If anything is missing, unclear,
or could be parallelized better,
create more subagents right now and delegate.

Be brutally honest about whether all work is complete,
then repeat until no issues are found.
```
</details>

<details>
<summary><strong>Reverse engineering</strong></summary>

```md
Continue reverse engineering.

1. Restate the MODEL + top UNKNOWNS.
2. Pick ONE highest-value next TARGET (why).
3. DEEP-DIVE: purpose, I/O + side effects, callers/callees, data/constants, ERROR paths.
4. If needed: STEP TRACE + PSEUDOCODE.
5. UPDATE: architecture, STATE MACHINE, ranked unknowns.
6. END: open questions, next target, PRESERVATION NOTE.

Continue until fully mapped and irreducible - stop only when zero meaningful unknowns remain.
```
</details>

<details>
<summary><strong>Implementation</strong></summary>

```md
Continue implementation using the existing reverse-engineering docs as source of truth.

1. Restate the PLAN + top RISKS/BLOCKERS.
2. Pick ONE highest-value next CHANGE to ship (why).
3. IMPLEMENT: exact edits (where/what), configs/migrations, deps, and TESTS.
4. If needed: STEP-BY-STEP rollout + fallback/rollback.
5. UPDATE: architecture/state deltas, invariants, ranked remaining backlog.
6. END: what shipped, verification results, new issues, next change, PRESERVATION NOTE.

Continue until the work is COMPLETE and stable - stop only when zero critical TODOs remain.
```
</details>

---

### Refactoring prompts

<details>
<summary><strong>JS/TS: Remove unused code & dependencies</strong></summary>

```md
Run `npx knip --fix` to automatically remove or refactor unused code and dependencies.

This helps clean up the codebase by eliminating unnecessary components.
```
</details>

<details>
<summary><strong>"Dependency-upgrade grinder" JS/TS: Update dependencies (Loopable)</strong></summary>

  Reference: <https://x.com/ericzakariasson/status/2064122375424373131?s=20>

```md
Loop through every outdated dependency one at a time until the list is empty and everything is green.

Start by using `npx npm-check-updates` to identify outdated packages and their latest versions. For each outdated package, update only that package in `package.json` using `npx npm-check-updates -u` with an appropriate package filter, then run `npm install` or `pnpm install` to install the updated version.

After each package update, check the changelog and release notes for breaking changes, make any required code or config adjustments, then run the full validation suite: build, type-check, and tests.

If anything breaks, fix the issue before moving on. If the package cannot be upgraded safely, revert that package's version change to the last working version, reinstall dependencies, and commit the working state.

Commit after each successful package upgrade with a clear message. Continue package by package until there are no outdated dependencies left and build, type-check, and tests all pass.
```
</details>

<details>
<summary><strong>Complete overhaul and update AGENTS.md</strong></summary>

  Reference: <https://x.com/0xSero/status/2064197526262083830>

```md
Review every file in this repository and identify incomplete abstractions, repetitive logic, duplicate types, unclear naming, unnecessary complexity, inefficient code paths, and meaningless or outdated comments.

Analyze the codebase structure, key components, data flow, interfaces, initialization steps, and interaction points.

Produce and execute a task list to clean up, complete, deduplicate, consolidate, and refactor the codebase into composable, consistent, and maintainable components while preserving existing functionality.

Improve readability and structure so coding agents such as Codex, Claude Code, and Copilot can understand and modify the project effectively.

Reduce technical debt, simplify logic, remove duplication, lower total lines of code where practical, and avoid over-fragmenting the project or creating unnecessary files.

Keep the directory structure minimal and intuitive. Align directory and file names with the material-icons naming convention where applicable.

Verify correctness with appropriate tests, checks, or validation steps.

Update AGENTS.md so it accurately reflects the current codebase. Keep it concise, current, and free from outdated or misleading information. Include only the essential modules, architecture overview, data flow, interaction points, initialization steps, and important guidance for future coding agents.
```
</details>

---

### Prompts for testing

<details>
<summary><strong>Whole app</strong></summary>

```md
Test the whole application thoroughly using the appropriate tool (CLI or browser).

For backend Node.js applications, write performant smoke tests using the built-in `node:test` module.

For frontend applications, use `vitest` for smoke testing.

Automatically identify and fix bugs and edge cases as you go.
```
</details>

<details>
<summary><strong>Specific feature</strong></summary>

```md
Test the feature thoroughly using the appropriate tool (CLI or browser).

For backend Node.js applications, write performant smoke tests using the built-in `node:test` module.

For frontend applications, use `vitest` for smoke testing.

Automatically identify and fix bugs and edge cases as you go.
```
</details>

<details>
<summary><strong>Flaky-test exterminator (Loopable)</strong></summary>

Reference: <https://x.com/ericzakariasson/status/2064122350866682100?s=20>

```md
run my test suite 20 times, collect every intermittent failure, fix or quarantine the flaky ones, and don't stop until you get 5 consecutive fully-green runs.
```
</details>


---

### Longer self-steer

<details>
<summary><strong>Codex exec plans</strong></summary>

Reference:

- <https://developers.openai.com/cookbook/articles/codex_exec_plans>
- <https://x.com/nummanali/status/2030944909599842705>
- <https://x.com/TroviAI/status/2062142198745833658>

```md
Create a comprehensive task list that covers every aspect of the plan so that it keeps you focused across compactions - at least 20 items or more if appropriate.
Do not stop until all changes have been verified.
```
</details>

---

### Reverse engineer repo/extensions

<details>
<summary><strong>Chrome Web Store reverse engineering PRD</strong></summary>

```md
Make a directory in "docs" called "{{REPO_NAME}}" and make a PRD with technical details (e.g. including system prompts), read/reverse-engineer this https://chromewebstore.google.com/detail/REPO_NAME
```
</details>

---

### Taste / UI

<details>
<summary><strong>Liquid glass</strong></summary>

Reference:

- https://x.com/EnoReyes/status/2031051418954842148

```md
xbox360 UI meets Spike Jonze's Her meets Liquid Glass
```
</details>

---

### Post-deploy

<details>
<summary><strong>Post-deploy error watch</strong></summary>

Reference:

- <https://x.com/ericzakariasson/status/2064122363185365430?s=20>

```md
I just shipped [thing]. /loop every 5m polling my error tracker and logs for new error groups on this service, and for each new one triage it and either push a hotfix or flag for rollback. Fall back to a heartbeat but wake immediately if a new error spikes.
```
</details>
