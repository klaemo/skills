---
name: remove-low-value-tests
description: Remove unjustified tests, preserve behavioral coverage, and simplify production seams made obsolete by the cleanup.
disable-model-invocation: true
---

# Remove low-value tests

Make tests justify themselves through observable regressions. Default scope: full repo — remove or consolidate unjustified tests, rewrite brittle assertions, simplify production seams made obsolete by the cleanup, and propose updates to testing guides from concrete findings. There is no deletion quota; zero changes is a valid outcome.

Honor narrower scopes: a directory, branch diff, uncommitted changes, **tests only** (edit only tests and test support; report seam/guide proposals), or **audit only** (no edits). You may read outside the scope to verify contracts, but keep edits in scope. Editing repo guides, committing, or opening a PR require a separate request.

Read the repository's testing and style guidance first (or use local patterns if none exists), skip generated/vendored files, and leave unrelated working-tree changes untouched.

## Judging tests

Evaluate each test against the observable regression it catches and whether a behavior-preserving refactor would break it:

- Cut or consolidate tests pinning copied constants, source structure, internal call sequences, fixture round trips, redundant parameterized variants, or duplicate failure coverage across layers (keep another layer only when it enforces a distinct contract, e.g. domain error → protocol response).
- Fix false-positive assertions (e.g. async checks that pass before work starts) and prefer observable UI behavior over incidental markup while keeping real visual contracts.
- Preserve distinct coverage for real boundaries (external payloads, auth, privacy), persistence/rollback, concurrency, streaming, accessibility, failure handling, boundary rejection of invalid inputs, and genuinely distinct small rules.
- When a contract is uncertain, keep the test and note it.

## Cleanup and validation

Consolidate useful assertions into a surviving test before removing duplicates; rewrite brittle assertions to check observable outcomes rather than weakening them into vacuous passes. Trace removed tests' imports into production and simplify seams (wrappers, injection points, test-only exports) only when made obsolete by the cleanup and confirmed unused by public or dynamic callers.

Run affected and repo-required checks; note exact scope, outcomes, and limitations.

## Report

Deliver a ledger in the response (or PR body when a PR is requested; label sections **Proposed** in audit-only mode):

- Short summary of scope, repo guidance used, exclusions, and findings (plus a tiny diff snippet only if it clarifies a rewrite).
- **Deleted files** and **removed/consolidated cases** with specific justifications (never just "low value") and where useful coverage survives.
- **Rewritten assertions** (file, brittle check → observable outcome).
- **Production seams & testing-guide proposals** (changes made, proposals, or why inspected seams stayed).
- **Validation** (commands, scope, outcomes, and limits).
