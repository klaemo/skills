---
name: council
description: Convene three models from three providers in parallel to review code, design, architecture, or UI, each with a score out of 10. Use when asked for a council.
---

# Council

You are the **lead**: members read and report; you judge findings and own the outcome. Default members: latest **Gemini Neon**, **GPT Sol**, and **Claude Fable** at **high** reasoning effort (user-named members replace defaults). Run one round per invocation; only run another round if asked, or if asked to raise the score.

## 1. Brief and convene

For code reviews, finish known fixes and run repo checks first. Write one self-contained brief sent verbatim to every member:
- The user's request, context (repo path + pinned base SHA, diff command, and in-scope untracked paths for code; relevant files, constraints, and options for design/arch/UI), out-of-scope note, check results, and the repo's code-review guide path if applicable (read-only rule and reply format outrank it).
- **Read-only rule**: read files and run read-only commands only; leave files, git state, and any PR unchanged; start no subagents.
- **Reply format**: `C1 [blocker|should|nit] <file:line or area> <claim>. Fix: <suggestion>`, then `SCORE: <n>/10` (against the stated request and constraints, plus what would raise it).

Resolve each member's newest model + high effort at runtime and launch all in parallel before waiting:
- **T3 Code**: `orchestrator_capabilities` (`search` if missing), then one `delegate_task` per member (`role: "review"` or `"design"`, model in `target`, effort in `target.options`).
- **Other harnesses**: one subagent per member.

Never substitute an unnamed model. Record unavailable members and run with the rest, but require at least **two** members (fewer → report incomplete and stop).

## 2. Weigh and summarize

Check findings against the code/facts as replies arrive (don't edit the checkout while any member is still reading). Record scores verbatim (`no score`, `no reply`, `unavailable`).

Report:
- **Agreements**: findings shared by 2+ members.
- **Disagreements**: each side in one line, plus your view and evidence.
- **Next steps**: confirmed in-scope work, smallest first. Put out-of-scope recommendations under **Follow-ups** to guard against scope creep.

Apply confirmed fixes only when the request asks for changes. Put a results table in the PR body if a PR exists (replacing any prior council section, above any trailing generated-by line), otherwise in the reply:

```markdown
## Council review

Rounds: 2

| Member | Round 1 | Round 2 |
| --- | --- | --- |
| <resolved model name> | 6/10 | 8/10 |
| <resolved model name> | no score | 9/10 |
```

## Further rounds

A score target ("get it to 9") is met when every recorded score reaches it; stop when met or no confirmed in-scope fix remains. For another round (when requested, or after applying fixes toward a score target), repeat with a fresh brief adding the updated diff and prior dispositions, and append a column to the table.
