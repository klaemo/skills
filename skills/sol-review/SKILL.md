---
name: sol-review
description: One read-only code review pass by the latest GPT Sol, each finding checked and fixed or declined, reported as a ledger. Use when the user or another skill asks for a Sol review.
---

# Sol review

One review pass of one change by one reviewer: the latest GPT Sol at high reasoning effort, or the model and effort the user names. The reviewer reads and reports; you judge every finding and make the fixes. The pass ends with a **ledger**, and fixes are not re-reviewed: another pass is a new invocation the user asks for.

## 1. Pin the scope

Run `git status --short`. The change runs from a pinned base to the working tree, untracked files included. Keep unrelated changes out by limiting the review to the change's paths; ask when they do not separate cleanly.

Pin the base after fetching. For a PR, take the merge base of `HEAD` and the PR's base branch; for a local branch, the merge base with the remote default branch, discovered from the remote rather than assumed; without a remote, the base the user names, or `HEAD` for uncommitted work alone. Fix one diff command, `git diff <base-sha> [-- <paths>]`, and use it everywhere below.

State the intent and what is out of scope in one or two sentences, from the request, the PR body, or the ticket. Discover the checks covering the change from repository instructions, package scripts, and CI configuration, run them, and fix failures the change causes before review. Confirm a failure is pre-existing by reproducing it on the base.

Completion: base SHA, diff command, intent, out-of-scope note, and each check's command and result are written down, and every failing check is shown to be pre-existing.

## 2. Write the brief

The reviewer gets no conversation history, so the brief is everything it knows. Search the repository's instructions and skill directories for its own code-review skill or guide. When one exists, the brief names its path and tells the reviewer to follow it as written; otherwise use the default below.

```text
Review only: read files and run read-only commands. Leave files, git state,
and any PR unchanged, and start no other reviewers or subagents.
Repo: <absolute path>
Base: <sha>
Change: <diff command>, plus untracked files from `git status --short`
Intent: <intent>
Out of scope: <out of scope>
Checks: <command: result, one per line>
<Repository guide: follow <path> as written.>  OR  <default review below>
```

Default review:

```text
Read the surrounding code and every caller of what changed. Report bugs,
callers the change breaks, and mismatch with the intent. Raise style only
when it hides a bug. Flag code, tests, or guards the intent does not need.
Severity: blocker = the intended case is wrong; should = a real bug callers
will hit within the intent; nit = everything else.
Reply:
VERDICT: APPROVE or CHANGES
S1 [blocker|should|nit] <file>:<line> <claim>. Fix: <suggestion>
(one line per finding, ids S1, S2, ... in order)
```

Completion: someone holding only the repository and the brief could perform the review.

## 3. Run the reviewer

Resolve the latest GPT Sol at runtime, and the effort option the harness exposes for it:

- **T3 Code**: call `orchestrator_capabilities` (with `search` when Sol is missing from the short list), pick the newest GPT Sol, and start one `delegate_task` with `role: "review"`, the brief as `task`, and the model and effort in `target`.
- **Other harnesses**: start one subagent on that model with the harness's subagent tool.

When no route reaches GPT Sol, stop and report the blocker. The reviewer is GPT Sol or nobody.

Record the model and effort the harness reports. Leave the scoped files untouched while the reviewer reads them, and wait for its completion notification. A timeout, an error, or an empty reply is a failed pass: report it as such.

Completion: one reply with a verdict and findings from the recorded model, or a reported failure.

## 4. Judge every finding

Read the cited code and reproduce the claim where you can, then grade severity yourself: another code path, a rare case, or a small cost is a nit whatever its label. Every outcome rests on evidence you gathered:

- **Fixed**: the claim holds. Make the smallest fix, preferring to delete or narrow over adding a helper, option, lock, or cache. Add one focused test that fails without the fix when the intent needs it.
- **Declined**: the claim fails, or it is out of scope or a nit not worth the diff. Cite the evidence (file:line, test, command output), or give one line and list it as a follow-up.

A finding you could not confirm is declined with what you checked. When the reviewer followed a repository guide whose output carries no ids, assign `S1`, `S2`, … in reply order.

Rerun the checks from step 1 and `git diff --shortstat <base-sha>`. When the fixes grew the diff beyond what the findings needed, cut back.

Completion: every finding id is fixed or declined with its evidence, and the checks pass after the fixes.

## 5. Report the ledger

Fixes stay in the working tree; committing and pushing belong to the caller. Report:

```markdown
## Sol review

One pass by <model> (<effort>) on `<diff command>`. Verdict: <APPROVE|CHANGES>.

| ID | Severity | Outcome | Reason |
| --- | --- | --- | --- |
| S1 | should | fixed | <what changed> |
| S2 | nit | declined | <evidence> |

Checks after fixes: <command: result>. Fixes were not re-reviewed.
```

Use your graded severity, noting the reviewer's label when you changed it. A reply with no findings still gets the header, verdict, and checks line.

Completion: the ledger accounts for every finding id and names the model, the effort, and the unreviewed state of the fixes.
