# Skills

My collection of agent skills.

Skills live in the `skills/` directory.

## Contents

| Skill | Description |
| --- | --- |
| [impossible-states](skills/impossible-states/SKILL.md) | Simplify a codebase by making invalid states unrepresentable, carrying parsed types through every consumer, and removing redundant defenses. Reads the repository's own coding standards and style guides before refactoring. |

## Install

```sh
npx skills add https://github.com/klaemo/skills --skill impossible-states --agent claude-code codex
```

The command installs the published version. To install from this checkout before publishing:

```sh
npx skills add . --skill impossible-states --agent claude-code codex
```

## Use

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
