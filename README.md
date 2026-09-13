# Skills

My collection of agent skills.

Skills live in the `skills/` directory.

## Contents

| Skill | Description |
| --- | --- |
| [impossible-states](skills/impossible-states/SKILL.md) | Simplify a codebase by making invalid states unrepresentable, carrying parsed types through every consumer, and removing redundant defenses. Reads the repository's own coding standards and style guides before refactoring. |
| [remove-low-value-tests](skills/remove-low-value-tests/SKILL.md) | Remove unjustified tests, preserve behavioral coverage, and simplify obsolete production seams. |

## Impossible states

Install the published version:

```sh
npx skills add https://github.com/klaemo/skills --skill impossible-states --agent claude-code codex
```

This skill runs only when you explicitly invoke it:

- Claude Code: `/impossible-states`
- Codex: `$impossible-states`

By default, the skill covers the full repository and implements the simplifications it finds, then reports the results. Ask for **audit only** to receive findings and proposals without code changes.

Add a scope after the skill name to focus on changes on the current branch, uncommitted changes, or a particular directory. You can combine scope with audit only:

```text
/impossible-states audit only
/impossible-states only the changes on this branch compared with main
/impossible-states audit only, uncommitted changes
/impossible-states only src/billing
```

In Codex, use `$impossible-states` with the same instructions.

## Remove low-value tests

Install the published version:

```sh
npx skills add https://github.com/klaemo/skills --skill remove-low-value-tests --agent claude-code codex
```

Invoke explicitly with `/remove-low-value-tests` in Claude Code or `$remove-low-value-tests` in Codex.

By default, it sweeps the full repository, removes or consolidates unjustified tests, rewrites brittle assertions, inspects production seams for justified simplification, and updates testing guidance from concrete findings. It reads applicable repository instructions and style guides, preserves distinct behavioral coverage, and reports a removal ledger and validation results. No deletion quota applies. Commits and PR publication require a request.

Use **audit only** for findings without file edits, **tests only** to restrict edits to tests and their support files, or name a directory or change scope. These options can be combined:

```text
/remove-low-value-tests audit only
/remove-low-value-tests only src/billing
/remove-low-value-tests tests only, changes on this branch compared with main
/remove-low-value-tests audit only, uncommitted changes
```

In Codex, use `$remove-low-value-tests` with the same instructions. Related code may be read to understand contracts, while edits stay within the requested scope.

## Install from a local checkout

From the repository root, replace `<skill-name>` with any skill listed above:

```sh
npx skills add . --skill <skill-name> --agent claude-code codex
```
