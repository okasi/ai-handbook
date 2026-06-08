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
<summary><strong>JS/TS: Update dependencies</strong></summary>

```md
Run `npx npm-check-updates -u` to update package.json with the latest dependencies.

Then, run `npm install` or `pnpm install` to install the updated versions.

Check the changelogs for any breaking changes, make required adjustments, and ensure nothing is broken.

If issues arise, revert the version changes to the last working one and reinstall dependencies.
```

</details>

<details>
<summary><strong>Complete overhaul and update AGENTS.md</strong></summary>

```md
Analyze the entire codebase and architecture to identify the key components, data flow, interfaces, initialization steps, and interaction points.

Refactor the codebase to improve readability, structure, clarity, and maintainability so coding agents such as Codex by OpenAI, Claude Code by Anthropic, and Copilot by GitHub can understand and modify it more effectively.

Simplify logic, clarify naming, reduce complexity, eliminate technical debt, and preserve existing functionality.

Optimize performance where practical by improving inefficient code paths, reducing unnecessary work, removing duplication, and lowering the total lines of code without over-fragmenting the project or creating too many separate files.

Keep the directory structure minimal and intuitive. Align directory and file names with the material-icons naming convention where applicable.

Verify correctness after changes through appropriate tests, checks, or validation steps.

Update AGENTS.md so it accurately reflects the current state of the codebase and free from tech debt.

Keep it concise and free from outdated or misleading information.

Include only the essential modules, architecture overview, data flow, interaction points, initialization steps, and any important guidance needed by future coding agents.
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

