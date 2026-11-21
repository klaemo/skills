---
name: lgtm
description: Land a PR, an ordered list of PRs, or a stack, babysit checks, follow a documented deployment, and stop their dev servers.
disable-model-invocation: true
---

# LGTM

Run only when the user explicitly invokes `lgtm`. Invocation authorizes rebasing, pushing the PR branches, fixing failures within the PRs' scope, merging when repository requirements pass, watching a documented deployment, and stopping the associated dev or preview servers. Carry this through without asking for the same authorization again.

## 1. Establish the target

The target is the PRs supplied with the invocation, or the current branch's PR. An ordered list ("merge #392 and then #396") lands in that order, each merged before the next begins; ask when that order conflicts with the stack's dependencies.

Read applicable `AGENTS.md`, `CLAUDE.md`, and linked guidance for checks, merge practice, stack tooling, post-merge deployment, and starting and stopping servers. Inspect the working tree, each PR's head and base, the remote default branch, repository merge settings, and review requirements. Register each PR with the host application's thread-linking tool when one exists.

Discover stack dependencies through the stack tooling the user or repository documents (a stack skill or CLI, and its status command). Without such tooling, follow PR base branches down to the default branch. Invocation authorizes landing a target's unmerged ancestors, bottom first; PRs above the target stay unmerged. Ask when scope is ambiguous, including sibling branches, or when an in-scope PR is draft or has blocking reviews.

Ask for the target when several fit or no PR exists. Preserve unrelated uncommitted changes; use an isolated checkout when needed. A PR already merged is recorded for server cleanup; one closed without merging is reported as a blocker.

Completion: an ordered landing list in which every PR still to land is open and ready, with its base, its head SHA, and for a stacked PR its fork point (the parent branch's head) recorded, or a question to the user.

## 2. Rebase and validate

Take the landing list one PR at a time. Fetch the latest default branch from the PR's target repository, discovered rather than assumed to be `main` or `origin`, and rebase the PR branch onto it. In a stack, retarget the PR to the default branch and replay only its own layer from the recorded fork point: `git rebase --onto <default> <fork-point>`. A plain rebase after a squash merge replays the parent's commits. Stack tooling may retarget and repair descendants instead; verify that the PR's base is the default branch and its diff holds only its own layer. For an intentionally different base outside a stack, clarify the destination.

Resolve conflicts using the PR's intended behavior and repository conventions; ask only when resolution requires a substantive product decision. Run the repository's required validation. Push with an explicit force-with-lease against the remote head recorded before rebasing; when the lease fails, inspect the concurrent changes before proceeding.

Completion: the remote head is the rebased, locally validated branch, and its SHA is recorded.

## 3. Babysit through merge

Wait on checks for the current remote head the way the harness provides. With a PR watcher (`watch_pull_request` in T3 Code), register the watch and end the turn; when it wakes you, re-read the live head and continue this step. Where the harness permits polling instead, use bounded waits and concise progress updates. With neither, report the pending checks and stop. Inspect failure logs, fix failures within scope, validate, and push. After every new commit or rebase, wait for checks on the new head; earlier green results do not carry over. Retry a transient infrastructure failure only when the evidence supports a retry. When the same failure persists after two remedies, or progress needs credentials, human review, or an out-of-scope change, stop the watcher and report the concrete blocker.

Before merging, refresh the PR's head, base, checks, and reviews, and confirm that required checks passed, applicable CI is green, required approvals are still valid, and blocking threads are resolved. When the base advanced and repository rules require an update, return to step 2. Merge through branch protection and merge queues as configured; bypass options such as `--admin` and weakened checks are off the table.

Use the repository's documented merge method; prefer squash when allowed and nothing else is specified. Bind each merge to the **verified head** SHA (on GitHub, `gh pr merge --match-head-commit <sha>`). Use stack merge tooling only when it preserves this binding; otherwise merge through the host CLI and repair descendants with the stack tooling, giving each repaired head fresh checks before its merge. When the head changes, re-evaluate its checks first. Use auto-merge or a merge queue only when it keeps this binding, so that a later push cannot merge unverified. Then keep watching until the host reports the PR merged; enabling auto-merge is a request, not a merge. Stop the watcher (`unwatch_pull_request` in T3 Code) whenever the PR is merged or the run hands back, blocked runs included.

Completion: the host reports every PR in the landing list merged, with its merge commit recorded.

## 4. Follow the deployment

When the repository documents a deployment that runs after merge, follow it through to production when the user asks for it or the repository's instructions prescribe verifying the deployment after a merge: find the deployment run for each landed merge that changes what is deployed, watch it to completion, and confirm from the documented health check or status source that the deployed version or SHA includes those merges. A deployment that waits on a manual promotion or approval is reported in that state. On failure, report the run and its failing step.

Completion: the production state is confirmed and recorded, the deployment's blocking or failing state is reported, or this run does not follow a deployment.

## 5. Stop the worktree's servers

After the last confirmed merge, sync stack metadata with the documented stack tooling, when there is one. Then stop dev and preview servers belonging to the landed PRs' worktrees or branches as the project's instructions describe, including any launcher wrapper's lifecycle. Select each process by a recorded process or session handle, or by a project-supported ownership check. On Linux, when discovery is needed, identify the listener for the known preview port with `ss -H -ltnp` and verify `/proc/<pid>/cwd` belongs to the intended worktree before terminating it.

Ownership decides termination: stop a recorded process, or a listener whose live working directory is the intended worktree. Pattern matching on a name, command line, or branch string (`pkill -f`, `pgrep | kill`) establishes no ownership. Leave shared proxies and other worktrees' servers running. When ownership cannot be established, leave the process running and report it. Keep branches and worktrees unless their removal is separately authorized.

Completion: each owned server has exited or its listener is gone, and every unresolved process is listed.

## 6. Report

List each PR link in merge order with its merge method, merge commit, and validation result. Report stack sync, the deployed state, and server cleanup. Distinguish a blocked PR, queued merge, pending deployment, or incomplete cleanup from a completed workflow. When the user named a thread to notify, send it this report (`t3_thread_send` in T3 Code, the harness's messaging tool elsewhere).
