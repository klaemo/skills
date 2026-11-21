---
name: lfg
description: Take a request to an open PR with a Sol review ledger and proof, delegating parts when the work spans several tickets.
disable-model-invocation: true
---

# LFG

Run only when the user explicitly invokes `lfg`. Invocation authorizes creating branches, committing, pushing new branches, running the app for proof, starting delegates, and opening PRs. The run ends at an open PR: landing it is `lgtm`, the user's separate approval.

Ask the user only when the goal is ambiguous in a way that changes the result, or when a change cannot be proven; use the harness's question tool so the thread shows it is waiting. Decide everything else with sensible defaults and list those decisions in the report.

## 1. Restate the intent

Write the intent in one or two lines: what changes, and what someone would observe once it works. Choose the mode: **orchestrated** when the work spans several tickets or the user asks to delegate (see [Orchestrated mode](#orchestrated-mode)), **single** otherwise.

Completion: the intent is stated and the mode chosen.

## 2. Implement

Fetch, then create a branch from the latest default branch (or the base the user names) and do all work there; the default branch receives no commits from this run. Read the repository's instructions for conventions, checks, and PR practice. Make the smallest change that works: fix the case the request names, without speculative options or adjacent refactors.

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

## Orchestrated mode

You become the **lead**: you split the work, delegate each part, and own the combined result.

1. Split the intent into parts, one per ticket or independent unit, in dependency order. Completion: every piece of the intent belongs to exactly one part.
2. Delegate each part to an implementer in its own checkout: in T3 Code, `delegate_task` with `role: "implementation"` and `isolation: "worktree"`; elsewhere, the harness's subagent tool with worktree isolation. The implementer gets no history, so its brief holds the overall intent, its part, its base branch, the absolute path of this file with the instruction to follow steps 2 to 5 (including `sol-review` and `proof`, which authorizes it to start the Sol reviewer), and a reply format: PR URL, head SHA, ledger, proof status. When an implementer's harness cannot start the reviewer, run `sol-review` on that part's branch yourself and add the ledger to its PR. A part that depends on another branches from that part's branch, and its PR targets it, forming a stack; use the repository's stack tooling when it documents one. Completion: every part has an implementer running.
3. Check each implementer's reply as it arrives: the PR exists, its body carries proof and a ledger, its diff stays within its part. Send gaps back to that part. Completion: every part has an open PR that passes this check.
4. Check the combined result against the original intent: read the combined diff (the top of a stack, or every PR together), and run the checks on it. Every piece of the intent is delivered, and no two parts overlap or contradict. Fix gaps in the owning part's PR. Completion: the combined result meets the intent.

## Report

List each PR link (in stack order, bottom first) with its intent, proof summary, ledger summary, and anything unproven. List the decisions you made for the user. State that nothing is merged and `lgtm` lands it.
