---
name: council
description: Council review, three models weighing in on code, design, architecture, or UI in parallel, each with a score out of 10. Use when the user or another skill asks for a council.
---

# Council

You are the **lead**. The members read and report; you judge their findings and own the outcome. Default members: the latest Gemini Neon, the latest GPT Sol, and the latest Claude Fable, each at high reasoning effort. Members the user names replace the defaults.

A **round** is one pass by every member. Run one round per invocation; a fresh round after fixes runs only when the user asks for one or asked you to raise the score.

## 1. Write the brief

For a code review, the members review the current implementation: finish known fixes and run the repository's checks first. Write one brief that every member receives verbatim. Members get no conversation history, so it carries:

- the request, in the user's words;
- the context: the repository's absolute path, and for code a pinned base SHA, one diff command, and the in-scope untracked files by path; for a design, architecture, or UI question the relevant files, constraints, and options on the table;
- what is out of scope, and the check results;
- the repository's own code-review guide by path, as review criteria within the read-only rule and reply format, when one exists and the question is a code review;
- the read-only rule: read files and run read-only commands, leave files, git state, and any PR unchanged, and start no subagents;
- the reply format: findings as `C1 [blocker|should|nit] <file:line or area> <claim>. Fix: <suggestion>`, then `SCORE: <n>/10` for how well the code or proposal meets the stated request and constraints, with what would raise it.

Completion: someone holding only the repository and the brief could answer it.

## 2. Convene the members

Resolve each member at runtime, choosing the newest model of its family and the high-effort option the harness exposes:

- **T3 Code**: call `orchestrator_capabilities` (with `search` for a family missing from the short list), then start one `delegate_task` per member with `role: "review"` (or `"design"`), the brief as `task`, the model in `target`, and the effort in `target.options`.
- **Other harnesses**: start one subagent per member with the harness's subagent tool.

Start every member before waiting on any. A member that cannot be reached stays empty: report it and run the round with the rest. A round needs two members; with fewer, report the council incomplete and stop. The council seats only the members it names.

Completion: every member is running or recorded as unavailable with the reason.

## 3. Weigh findings as they arrive

When a reply arrives, check its findings against the code and facts at once, while the others still run. Give each finding a disposition: confirmed, rejected with evidence, or out of scope. Leave the checkout unchanged while any member still reads it.

Record each score exactly as given. A reply without one gets `no score`, a member that errors or times out gets `no reply`, and one that could not be reached gets `unavailable`.

Completion: every member has replied, failed, or was unavailable, and every finding has a disposition.

## 4. Summarize

Report:

- **Agreements**: findings or recommendations two or more members share.
- **Disagreements**: each side in one line, with your view and its evidence.
- **Next steps**: confirmed work, smallest first. Recommendations reaching beyond the request's intent go under **Follow-ups**; holding that line against **scope creep** is the lead's job.

Apply confirmed fixes when the request asks for changes (fix what the council finds, raise the score); otherwise present them as next steps.

Put a results table in the PR body when the work has a PR, replacing any earlier council section and placed above a trailing generated-by line; otherwise in the reply. One row per member, one column per round:

```markdown
## Council review

Rounds: 2

| Member | Round 1 | Round 2 |
| --- | --- | --- |
| <resolved model name> | 6/10 | 8/10 |
| <resolved model name> | no score | 9/10 |
```

Completion: the summary covers every confirmed finding, and the table sits in the PR body or the reply with every cell filled from a member's actual reply.

## Further rounds

A score target ("get it to 9") is met when every recorded score reaches it. Stop when the target is met or no confirmed in-scope fix remains. When the user asks for a fresh round, or asked you to raise the score and you applied fixes, repeat steps 1 to 4 with a new brief that adds the current diff and the previous round's dispositions, and add a column to the table.
