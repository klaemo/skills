---
name: impossible-states
description: Simplify a codebase by making impossible states unrepresentable and carrying parsed types through every consumer.
disable-model-invocation: true
---

# Impossible states

Simplify the requested scope (default: full repo) in the "parse, don't validate" tradition: parse external input once, preserve the invariant in its type, and let interior code consume that type. For an **audit only** request, report proposals without editing files.

Follow repository coding/architecture guidance and local patterns; skip generated, vendored, and migration files. Don't publish a PR, edit repository guides, or delete existing reports unless asked.

## What to look for

Trace data from external boundaries through domain logic, persistence, transport, and consumers:

- Repeated validation of already-parsed values, or parallel schemas/types.
- Optional-field bags and independent flags that admit contradictory states.
- Return types broader than an operation can actually produce.
- Caller-supplied defaults that belong in the creation operation.
- Test-only seams, unused exports, and redundant helpers.

Prefer idiomatic stronger representations (discriminated unions, required per-state payloads, nonempty structures, schema-derived types, or parser-created brands rather than casts) that delete downstream machinery rather than relocating complexity. Stored invariants require real backing (DB constraints, controlled writes, or versioned decoders) — a row type alone isn't proof.

## Following through

Carry each stronger type through every caller, response, UI state, fixture, and test — adding a parser without removing obsolete downstream defenses is unfinished. Preserve external and serialized contracts (including generated or LLM schemas) and historical compatibility unless in scope.

Keep checks that protect real possibilities:
- Authorization, concurrent writes, and conditional updates (including after external calls).
- Independent storage reads, network responses, historical payloads, and SDKs that don't validate at runtime.
- Duplicate, incomplete, or out-of-order external events, and changing resource ownership.

## Verification and report

Test invalid input at the rejecting boundary, build valid fixtures through real parsers/constructors, and drop now-impossible interior tests without losing boundary rejection coverage. Run the repository's checks and note any failures or untested paths.

Report in the response (unless asked for a file), distinguishing implemented work from audit-only or blocked proposals:
- Scope covered, repo guidance used (or note if none), and exclusions/gaps.
- Proposals vs. results (stronger shape, affected consumers, machinery removed).
- Boundary checks that remain necessary and why.
- Verification results and limitations.
