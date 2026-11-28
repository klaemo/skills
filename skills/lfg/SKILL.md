---
name: lfg
description: Take a request to an open PR with a Sol review ledger and proof, delegating parts when the work spans several tickets.
disable-model-invocation: true
---

# LFG

Run only on explicit invocation. Invocation authorizes creating branches, committing, pushing new branches, running the app for proof, starting delegates, and opening PRs. **Never merge** — `lgtm` lands the PR.

Ask the user (via the harness question tool) only when the goal is genuinely ambiguous or proof needs user-only access or decisions. Decide everything else sensibly and note those decisions in the report.

## 1. State intent and choose mode

State the intent in 1–2 lines (what changes and what's observable). If the work spans multiple tickets or the user asks to delegate, read [`ORCHESTRATION.md`](ORCHESTRATION.md) and follow it instead of steps 2–5.

## 2. Implement

Work on a feature branch: continue on the current branch if already on one (leaving unrelated uncommitted edits out), or fetch and branch from the default branch's latest commit (or user-named base). Never commit to the default branch. Record the PR base (existing PR base, user-named base, or default branch).

Make the smallest change that works, run the repo's checks, and fix regressions (confirm any remaining failure is pre-existing on the base). Commit the change.

## 3. Review with `sol-review`

Run `sol-review` against the recorded base. Commit its fixes and keep the ledger for the PR body. If the review pass fails without a ledger, stop before opening a PR and report the blocker.

## 4. Prove with `proof`

If UI or observable behavior changed, run `proof` against the recorded base (with no PR yet, it returns media paths, captions, and command evidence). Otherwise note why no proof applies.

## 5. Open the PR

Push and open (or update) the PR against the recorded base following repo conventions. Include:
- Intent and short summary
- The `sol-review` ledger
- Checks run and results
- Last, the `## Proof` section per `proof` (or why none applies)

## Report

List each PR link (stack order, bottom first) with its intent, proof summary, ledger summary, anything unproven, and decisions made. Note that nothing is merged and `lgtm` lands it.
