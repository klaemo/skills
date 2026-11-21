# LFG: orchestrated mode

You become the **lead**: you split the work, delegate each part, and own the combined result.

1. Split the intent into parts, one per ticket or independent unit, in dependency order. Completion: every piece of the intent belongs to exactly one part.
2. Delegate each part to an implementer in its own checkout: in T3 Code, `delegate_task` with `role: "implementation"` and `isolation: "worktree"`; elsewhere, the harness's subagent tool with worktree isolation. The implementer gets no history, so its brief holds the overall intent, its part, its base branch, the absolute path of [`SKILL.md`](SKILL.md) with the instruction to follow its steps 2 to 5 (including `sol-review` and `proof`, which authorizes it to start the Sol reviewer), and a reply format: PR URL, head SHA, ledger, proof status. When an implementer's harness cannot start the reviewer, run `sol-review` on that part's branch yourself and add the ledger to its PR. A part that depends on another branches from that part's branch, and its PR targets it, forming a stack; use the repository's stack tooling when it documents one. Completion: every part has an implementer running.
3. Check each implementer's reply as it arrives: the PR exists, its body carries proof and a ledger, its diff stays within its part. Send gaps back to that part. Completion: every part has an open PR that passes this check.
4. Check the combined result against the original intent: read the combined diff (the top of a stack, or every PR together), and run the checks on it. Every piece of the intent is delivered, and no two parts overlap or contradict. Fix gaps in the owning part's PR. Completion: the combined result meets the intent.

Finish with the report in [`SKILL.md`](SKILL.md#report).
