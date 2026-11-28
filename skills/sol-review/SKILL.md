---
name: sol-review
description: Run one read-only code review pass by the latest GPT Sol, with the caller judging and fixing or declining each finding in a ledger. Use when asked for a Sol review.
---

# Sol review

One read-only review pass of one change by the latest **GPT Sol** at **high** reasoning effort (unless the user names another model or effort). The reviewer reads and reports; you judge every finding and make fixes. Fixes stay in the working tree (don't commit or push) and are **not** re-reviewed — another pass requires a new invocation.

## 1. Prepare scope and checks

Pin the diff from a base SHA to the working tree plus in-scope untracked files (keep unrelated changes out via path filters; ask if they don't separate cleanly):
- Base: caller-supplied base/scope, `HEAD` for uncommitted-only, PR base merge-base, or remote default merge-base (after fetching).
- Run repo checks and fix failures caused by the change before reviewing (confirm remaining failures are pre-existing on the base).

## 2. Run the reviewer

Resolve the newest GPT Sol + high effort (or user override) at runtime:
- **T3 Code**: `orchestrator_capabilities` (with `search` if missing), then `delegate_task` with `role: "review"`, the brief as `task`, model in `target`, effort in `target.options`.
- **Other harnesses**: subagent tool on that model.

If that model cannot be reached, stop and report the blocker — never fall back to another model. Leave scoped files untouched while the reviewer runs. A terminal error or empty reply is a failed pass (report failure, no ledger).

Give the reviewer a self-contained brief containing:
- **Read-only rule**: read files and run read-only commands only; leave files, git state, and any PR unchanged; start no subagents.
- **Context**: repo absolute path, base SHA, exact `git diff <base-sha> [-- <paths>]` command, in-scope untracked paths, 1–2 sentence intent, out-of-scope note, and check results.
- **Criteria**: path to the repo's own code-review skill/guide if one exists (the read-only rule and reply format outrank it); otherwise: read surrounding code and callers; report bugs, broken callers, intent mismatch, and unneeded code/tests/guards; raise style only when hiding a bug. Severity: `blocker` (intended case wrong), `should` (real bug callers will hit within intent), `nit` (else).
- **Reply format**: `VERDICT: APPROVE|CHANGES`, then one line per finding: `S1 [blocker|should|nit] <file>:<line> <claim>. Fix: <suggestion>`.

## 3. Judge findings and report the ledger

Verify every finding against the code (reproduce when possible) and grade severity yourself by demonstrated impact within the intent:
- **fixed**: smallest fix (prefer deleting/narrowing over new abstractions); add a focused test if the intent needs it.
- **declined**: cite evidence (`file:line`, test, or command output), or note out-of-scope/nit items as follow-ups.

Rerun checks from step 1 and ensure fixes stay minimal. Report (even when there are no findings):

```markdown
## Sol review

One pass by <model> (<effort>) on `<diff command>`. Verdict: <APPROVE|CHANGES>.

| ID | Severity | Outcome | Reason |
| --- | --- | --- | --- |
| S1 | should | fixed | <what changed> |
| S2 | nit | declined | <evidence> |

Checks after fixes: <command: result>. Fixes were not re-reviewed.
```

Use your graded severity (noting the reviewer's label if you changed it).
