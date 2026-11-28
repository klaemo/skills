# LFG: orchestrated mode

As **lead**, split the work, delegate each part, and own the combined result:

1. **Plan**: split the intent into one part per ticket or independent unit and record dependencies.
2. **Delegate ready parts**: whenever a part's dependencies all have open PRs, launch an implementer in its own worktree (`delegate_task` with `role: "implementation"` and `isolation: "worktree"` in T3 Code; worktree subagent elsewhere). Run independent parts in parallel. Stack dependent parts on their dependency's pushed branch (using repo stack tooling if documented). Give each implementer a self-contained brief:
   - Overall intent, its part, and what belongs to other parts.
   - Its exact base branch and base SHA — tell it to fetch and branch from that SHA before editing (delegated worktrees start from the lead's commit).
   - The absolute path of [`SKILL.md`](SKILL.md) to run steps 2–5 against that base (authorizing `sol-review`).
   - Fallback: if its harness cannot start `sol-review`, stop after committing and pushing so the lead runs steps 3–5 itself.
   - Reply with: PR URL (or branch), head SHA, ledger, and proof status.
3. **Verify each part**: as replies arrive, check that the PR exists, stays in scope, and has proof and a ledger. Send gaps back to that implementer, or run steps 3–5 yourself on fallback branches, then unblock dependent parts.
4. **Verify the combined result**: in one checkout holding every part's final head in dependency order (top of stack, or a scratch merge of independent parts), read the combined diff, run checks, and confirm the full intent with no overlaps or contradictions. Fix gaps in the owning PR and refresh affected proof/PR-body entries.

Finish with the report in [`SKILL.md`](SKILL.md#report).
