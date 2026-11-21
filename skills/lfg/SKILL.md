---
name: lfg
description: Take a request to an open PR with a Sol review ledger and proof, delegating parts when the work spans several tickets.
disable-model-invocation: true
---

# LFG

Run only when the user explicitly invokes `lfg`. Invocation authorizes creating branches, committing, pushing new branches, running the app for proof, starting delegates, and opening PRs. The run ends at an open PR: landing it is `lgtm`, the user's separate approval.

Ask the user only when the goal is ambiguous in a way that changes the result, or when a change cannot be proven; use the harness's question tool so the thread shows it is waiting. Decide everything else with sensible defaults and list those decisions in the report.

## 1. Restate the intent

Write the intent in one or two lines: what changes, and what someone would observe once it works. Choose the mode: **orchestrated** when the work spans several tickets or the user asks to delegate, **single** otherwise. In orchestrated mode, read [`ORCHESTRATION.md`](ORCHESTRATION.md) and follow it in place of steps 2 to 5.

Completion: the intent is stated and the mode chosen.

## 2. Implement

Work on a feature branch. When the checkout is already on one, as in a thread's own worktree, continue there and keep the work it holds. On the default branch, fetch and create a branch from its latest commit, or from the base the user names. The default branch receives no commits from this run. Read the repository's instructions for conventions, checks, and PR practice. Make the smallest change that works: fix the case the request names, without speculative options or adjacent refactors.

Run the repository's checks and fix what the change breaks. Confirm any remaining failure is pre-existing by reproducing it on the base.

Completion: the change is committed on the branch, and every check passes or has a failure shown to be pre-existing.

## 3. Review with `sol-review`

Invoke `sol-review` on the branch against its base. Commit the fixes it leaves in the working tree and keep its ledger for the PR body.

Completion: the ledger is in hand, its fixes are committed, and the checks pass.

## 4. Prove with `proof`

When the diff changes UI or observable behavior, invoke `proof`. With no PR yet, it returns file paths and captions for the PR body. When the diff has no observable effect, record why; the checks are its evidence.

Completion: every changed surface has proof, or the PR body will say why none applies or which change could not be proven.

## 5. Open the PR

Push the branch and open the PR against its base, following the repository's PR conventions (template, title style, draft policy). The body carries:

- the intent and a short summary of the change;
- a `## Proof` section with the captures attached the way `proof` delivers them, or the reason none applies;
- the `sol-review` ledger;
- the checks run and their results.

Completion: the PR is open against the intended base and its body carries the proof and the ledger.

## Report

List each PR link (in stack order, bottom first) with its intent, proof summary, ledger summary, and anything unproven. List the decisions you made for the user. State that nothing is merged and `lgtm` lands it.
