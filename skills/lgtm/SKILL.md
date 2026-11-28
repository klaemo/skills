---
name: lgtm
description: Land a PR, an ordered list of PRs, or a stack, babysit checks, follow a documented deployment, and stop their dev servers.
disable-model-invocation: true
---

# LGTM

Run only on explicit invocation. Invocation authorizes rebasing, pushing PR branches, fixing in-scope failures, merging once repo requirements pass, watching a documented deployment (when asked or repo-prescribed), and stopping owned dev/preview servers — carry this through without re-asking.

Read repo instructions (`AGENTS.md`, `CLAUDE.md`, linked guides) for checks, merge method, stack tooling, deployment, and server lifecycle.

## 1. Establish the landing list

Target the named PRs or the current branch's PR. Land an ordered list strictly in order (ask if it conflicts with stack dependencies).

Discover stack dependencies via documented stack tooling, or by following PR base branches down to the default branch. Land unmerged ancestors bottom-first; leave PRs above the target unmerged. Ask when scope is ambiguous (e.g. sibling branches), no PR fits, or an in-scope PR is draft or has blocking reviews. Record already-merged PRs for server cleanup; treat closed-without-merge PRs as blockers.

For stacked PRs, record each PR's fork point (parent branch head) before merging its parent.

## 2. Rebase, validate, and babysit through merge

Process one PR at a time:

1. Fetch the target repo's default branch (discover it rather than assuming `main`/`origin`) and rebase onto it. For a stacked PR whose parent was squash-merged, retarget to the default branch and replay only its own layer (`git rebase --onto <default> <fork-point>`), or use stack tooling and verify the base and diff contain only this layer. Clarify intentionally non-default bases outside a stack.
2. Run required local validation and push with `--force-with-lease` against the recorded pre-rebase remote head (inspect concurrent changes if the lease fails).
3. Wait for checks on the current remote head: in T3 Code, call `watch_pull_request` and end the turn (unwatch on merge or any handback, including blocked runs); elsewhere use bounded polling, or report pending checks and stop if neither is available.
4. Fix in-scope CI failures, validate, push, and wait on the new head. Stop and report a concrete blocker if the same failure persists after two fixes, or if progress needs credentials, human review, or out-of-scope changes.
5. Before merging, verify the live head, base, green checks, valid approvals, and resolved blocking threads (return to step 1 if the base advanced and repo rules require an update). Never bypass branch protection or merge queues (`--admin`, weakened checks).
6. Merge with the repo's documented method (default: squash when allowed), **bound to the verified head SHA** (e.g. `gh pr merge --match-head-commit <sha>`). Use stack merge tooling, auto-merge, or a merge queue only if they preserve head-SHA binding, and keep watching until actually merged.

## 3. Post-merge: deploy, cleanup, and report

- **Deployment** (only when asked or repo-prescribed): watch the deployment run for merges affecting deployed code and confirm via the documented health/status check; report manual promotion gates or failures.
- **Stack sync & server cleanup**: sync stack metadata if documented, then stop dev/preview servers for the landed worktrees/branches per repo instructions. Terminate **only** by recorded handle or verified ownership (on Linux, e.g. `ss -H -ltnp` + `/proc/<pid>/cwd` matching the worktree) — never `pkill -f` or `pgrep | kill`. Leave shared proxies, other worktrees' servers, and unverified processes running (report unresolved ones). Keep branches and worktrees unless separately authorized.
- **Report**: list each PR link in merge order (method, merge commit, validation), stack sync, deployed state, server cleanup, and any blocked/queued/pending/incomplete items. If the user named a thread to notify, send it this report (`t3_thread_send` in T3 Code, or the harness's messaging tool).
