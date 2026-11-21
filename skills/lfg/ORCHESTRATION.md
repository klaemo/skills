# LFG: orchestrated mode

You become the **lead**: you split the work, delegate each part, and own the combined result.

1. Split the intent into parts, one per ticket or independent unit, and name for each the parts it depends on. Completion: every piece of the intent belongs to exactly one part, and the dependency order is written down.
2. Delegate each **ready** part, one whose dependencies all have an open PR, to an implementer in its own checkout: in T3 Code, `delegate_task` with `role: "implementation"` and `isolation: "worktree"`; elsewhere, the harness's subagent tool with worktree isolation. Independent parts run in parallel. A dependent part builds on its dependency's pushed branch, and its PR targets that branch, forming a stack; use the repository's stack tooling when it documents one. The implementer gets no history, so its brief holds:
   - the overall intent, its part, and what belongs to other parts;
   - its base branch and base SHA, with the instruction to fetch, create its branch from that SHA before any edit, and confirm `git merge-base HEAD <base-sha>` returns it, since a delegated checkout starts from the lead's commit;
   - the absolute path of [`SKILL.md`](SKILL.md), with the instruction to follow its steps 2 to 5 using that base as the recorded PR base (this authorizes it to start the Sol reviewer);
   - the fallback: when its harness cannot start the reviewer, it stops after step 2 and replies with the committed, pushed branch;
   - a reply format: PR URL or branch, head SHA, ledger, proof status.

   For a part handed back without review, run steps 3 to 5 of `SKILL.md` on its branch yourself, so the proof shows the reviewed result. When a part cannot get its base, report it blocked. Completion: every part has an implementer running, an open PR, or a reported blocker, and no part waits on a dependency that has an open PR.
3. Check each implementer's reply as it arrives: the PR exists, its body carries proof and a ledger, its diff stays within its part. Send gaps back to that part, then return to step 2 for the parts this one unblocks. Completion: every part has an open PR that passes this check, or a reported blocker.
4. Check the combined result against the original intent in one checkout that holds every part's final head in dependency order (the top of a stack, or a scratch merge of independent parts): read the combined diff and run the checks there. Every piece of the intent is delivered, and no two parts overlap or contradict. Fix a gap in the owning part's PR, then validate its new head and refresh the proof and PR-body entries the fix affects. Completion: the combined result meets the intent, and every PR body matches its current head.

Finish with the report in [`SKILL.md`](SKILL.md#report).
