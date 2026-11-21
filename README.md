# Skills

My collection of agent skills.

Skills live in the `skills/` directory.

## Contents

| Skill | Description |
| --- | --- |
| [impossible-states](skills/impossible-states/SKILL.md) | Simplify a codebase by making invalid states unrepresentable, carrying parsed types through every consumer, and removing redundant defenses. |
| [remove-low-value-tests](skills/remove-low-value-tests/SKILL.md) | Remove unjustified tests, preserve behavioral coverage, and simplify obsolete production seams. |
| [lgtm](skills/lgtm/SKILL.md) | Land a PR, an ordered list of PRs, or a stack, babysit checks, follow a documented deployment, and stop their dev servers. |
| [lfg](skills/lfg/SKILL.md) | Take a request to an open PR with a Sol review ledger and proof, delegating parts when the work spans several tickets. |
| [sol-review](skills/sol-review/SKILL.md) | Run one read-only code review pass by the latest GPT Sol, check every finding, and report a ledger. |
| [proof](skills/proof/SKILL.md) | Capture evidence that a change works: labelled before/after captures, a screen recording, or command output, attached to the PR. |
| [council](skills/council/SKILL.md) | Ask three models from three providers to weigh in on code, design, architecture, or UI, each with a score out of 10. |

## impossible-states

Simplify a codebase by making invalid states unrepresentable, carrying parsed types through every consumer, and removing redundant defenses.

```sh
npx skills add https://github.com/klaemo/skills --skill impossible-states
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

> [!TIP]
> After running the skill, ask your agent to update your project's coding and testing guides based on what the skill discovered.

## remove-low-value-tests

Remove unjustified tests, preserve behavioral coverage, and simplify production seams made obsolete by the cleanup.

```sh
npx skills add https://github.com/klaemo/skills --skill remove-low-value-tests
```

Invoke explicitly with `/remove-low-value-tests` in Claude Code or `$remove-low-value-tests` in Codex.

By default, it sweeps the full repository, removes or consolidates unjustified tests, rewrites brittle assertions, inspects production seams for justified simplification, and reports testing-guidance proposals from concrete findings. It reads applicable repository instructions and style guides, preserves distinct behavioral coverage, and reports a removal ledger and validation results. No deletion quota applies. Guide updates, commits, and PR publication require a request.

Use **audit only** for findings without file edits, **tests only** to restrict edits to tests and their support files, or name a directory or change scope. These options can be combined:

```text
/remove-low-value-tests audit only
/remove-low-value-tests only src/billing
/remove-low-value-tests tests only, changes on this branch compared with main
/remove-low-value-tests audit only, uncommitted changes
```

In Codex, use `$remove-low-value-tests` with the same instructions. Related code may be read to understand contracts, while edits stay within the requested scope.

> [!TIP]
> After running the skill, ask your agent to update your project's coding and testing guides based on what the skill discovered.

## lgtm

Land a PR, an ordered list of PRs, or a stack: rebase, validate, babysit checks, merge bound to the verified head commit, follow a documented deployment to production when asked, and stop the landed worktrees' dev servers.

```sh
npx skills add https://github.com/klaemo/skills --skill lgtm
```

Invoke explicitly with `/lgtm` in Claude Code or `$lgtm` in Codex. Invocation authorizes rebasing, pushing the PR branches, fixing failures within the PRs' scope, and merging once repository requirements pass. Branch protection and merge queues are always respected.

It discovers checks, the merge method, stack tooling, deployment, and server rules from the repository's own instructions. Without stack tooling, it follows PR base branches and lands unmerged ancestors bottom first. Name the PRs, an order, or a thread to notify:

```text
/lgtm
/lgtm #392
/lgtm merge #392 and then #396
/lgtm #392, follow the deployment to production, then tell the release thread
```

## lfg

Take a request to an open PR: restate the intent, implement the smallest change on a feature branch, run the checks, review it with [sol-review](#sol-review), prove it with [proof](#proof), and open the PR with the proof and the review ledger in its body.

```sh
npx skills add https://github.com/klaemo/skills --skill lfg
```

It calls `sol-review` and `proof`, so install those as well.

Invoke explicitly with `/lfg` in Claude Code or `$lfg` in Codex. It never merges; run [lgtm](#lgtm) to land the PR.

When the work spans several tickets, or you ask it to delegate, the agent becomes the lead: each part goes to an implementer in its own checkout, gets its own review and proof, and dependent parts are stacked. The lead finishes by checking the combined result against the original request.

```text
/lfg add a confirmation dialog to the archive action
/lfg tickets ENG-12, ENG-13 and ENG-14, delegate each
```

## sol-review

Run exactly one read-only code review pass by the latest GPT Sol at high reasoning effort. The calling agent checks every finding itself, fixes or declines it with evidence, reruns the checks, and reports a ledger. Fixes are not re-reviewed, and nothing is pushed.

```sh
npx skills add https://github.com/klaemo/skills --skill sol-review
```

Invoke with `/sol-review` in Claude Code or `$sol-review` in Codex, or ask for a Sol review; other skills such as [lfg](#lfg) call it too. When the repository has its own code-review skill, the reviewer follows it. Name another model or effort to override the default:

```text
/sol-review
/sol-review the changes on this branch compared with main, at max effort
```

The reviewer runs on another provider: in T3 Code through its delegation tools, elsewhere through the harness's subagent tools. When GPT Sol cannot be reached, the skill reports that instead of using another model.

## proof

Capture proof that a change works: a labelled before/after collage per changed surface for static UI, a screen recording for motion or multi-step flows, and a reproduction or command output for other behavior. Every state the diff touches, such as a tooltip, a confirmation dialog, dark mode, or mobile width, gets its own capture.

```sh
npx skills add https://github.com/klaemo/skills --skill proof
```

Invoke with `/proof` in Claude Code or `$proof` in Codex, or ask for proof; agents also reach for it before reporting a UI or behavior change done. It drives the app with the project's verification skill or documented method when one exists, otherwise with a browser automation tool. The proof is shown in the conversation and attached to the PR body when a PR exists (GitHub attachments need `gh` 2.99 or later). Evidence files stay outside the repository.

## council

Convene three models, by default the latest Gemini Neon, GPT Sol, and Claude Fable at high reasoning effort, to weigh in read-only and in parallel on a code review or a design, architecture, or UI question. Each member returns findings and a score out of 10. The agent checks findings as they arrive and summarizes agreements, disagreements, and next steps, keeping scope to the request.

```sh
npx skills add https://github.com/klaemo/skills --skill council
```

Invoke with `/council` in Claude Code or `$council` in Codex, or ask for a council. Name members to replace the defaults. A fresh round after fixes runs only when you ask for one or ask to raise the score. Results go in a table in the PR body when the work has a PR, otherwise in the reply.

```text
/council review this branch
/council should the sync queue live in the worker or the database?
/council with GPT Sol and Claude Fable, get the score to 9
```

## Install from a local checkout

From the repository root, replace `<skill-name>` with any skill listed above:

```sh
npx skills add . --skill <skill-name>
```
