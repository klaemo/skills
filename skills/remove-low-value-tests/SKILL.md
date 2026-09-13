---
name: remove-low-value-tests
description: Remove unjustified tests, preserve behavioral coverage, and simplify production seams made obsolete by the cleanup.
disable-model-invocation: true
---

# Remove low-value tests

Make tests justify their presence through observable regressions. By default, inspect the full repository, implement justified test cleanup, inspect resulting production simplifications, and update testing guidance from concrete findings. Counts summarize the result; they are never deletion targets. Finding no justified changes is a valid outcome.

Honor narrower requests: a directory, branch diff, uncommitted changes, tests only, or audit only. For audit only, investigate and report proposals without editing files. For tests only, limit edits to tests and their support files; report production and guidance proposals separately. Read related code outside the requested scope when needed to establish contracts, but keep edits within scope. Commit or publish a PR only when requested.

## 1. Establish scope and repository rules

Discover and read applicable repository and directory instructions, testing policies, coding standards, style guides, and contribution guidance before judging tests. Search both filenames and document contents, including hidden documentation directories and applicable ancestor instructions. Follow relevant linked guidance, resolving relative links from the containing document. Assume no particular filenames or locations. If guidance is absent or inaccessible, record that and use established local patterns without inventing repository rules.

Record the requested scope and comparison base where relevant, existing working-tree changes, protected/generated/vendor areas, test commands, and required checks. Preserve unrelated work. Establish the relevant test baseline where feasible; distinguish existing failures from later regressions.

Completion: applicable guidance is read or its absence recorded, and the inspection plan accounts for every area in scope, exclusions, commands, and baseline limitations.

## 2. Evaluate tests against contracts

Inventory the whole requested scope using the repository's test discovery/configuration as well as file searches. Inspect tests together with production implementations, real callers, and neighboring coverage. Search hits are candidates, not deletion evidence.

For each candidate, identify the observable regression it catches and whether a behavior-preserving refactor would require assertion changes. Changing a genuine contract legitimately changes its tests. Judge overlapping tests by the failures they catch; another layer earns its place through a distinct contract, such as translating a domain failure into a protocol response.

Apply these distinctions:

- Copied constants, source structure, internal call sequences, and fixture round trips need evidence of an independent contract. Exact values and mock calls remain valuable for external payloads, authorization, privacy, or other real boundary requirements.
- Preserve distinct persistence, rollback, concurrency, streaming, accessibility, and failure-handling coverage. Test doubles and dependency injection at real boundaries can remain useful.
- Small tests and simple functions can protect meaningful rules. Each parameterized variant should earn its place through a distinct rule or failure mode.
- Before dismissing synthetic invalid states, verify that real callers or enforced boundaries exclude them; retain coverage where rejection occurs.
- Check assertions for false positives. An asynchronous completion check should prove the operation started before accepting a state that was already true initially. Prefer observable UI behavior over incidental markup while retaining actual visual contracts.

Completion: every in-scope test area is inspected or explicitly marked incomplete. Each proposed removal or rewrite names the pinned implementation detail, redundant failure coverage, or missing regression value; duplicate findings identify surviving coverage. Retain uncertain cases and report the unresolved contract.

## 3. Implement justified cleanup

In edit mode, remove unjustified cases and orphaned fixtures. Consolidate useful assertions into the surviving scenario before deleting overlap. Rewrite valuable scenarios to assert observable outcomes, with assertions that would fail on the identified regression. Renaming or weakening a test into a vacuous pass is not a repair.

Trace removed tests' imports into production and inspect remaining callers, exports, wrappers, injection points, and helpers. Check public consumers and dynamic/configured uses where applicable; a missing local import alone does not establish that an API is unused. Simplify only seams made obsolete by this cleanup with evidence that their real obligations remain satisfied. Record why retained seams are still needed when no simplification is justified.

Update the discovered testing guidance with reusable lessons supported by findings, preserving the repository's boundary and integration rules. If no guide exists, choose a location consistent with its documentation conventions. Make guidance changes only within the requested edit scope.

Completion: authorized changes preserve each valuable scenario, every removal has a ledger entry, affected seams have a supported disposition, and guidance changes or proposals follow the findings. In audit mode, produce these as proposals only.

## 4. Validate and review

Run affected behavioral checks and repository-required checks using discovered commands. Review the final diff against the identified contracts and removal ledger, including fixtures and production changes. After further edits, rerun checks whose evidence those edits invalidate. Record actual commands, scope, outcomes, retries, and limitations; distinguish demonstrated causes from uncertainty.

Completion: every changed behavior has validation evidence or an explicit limitation, required checks have outcomes or blockers, and every deletion is accounted for without accidentally losing a distinct contract. A blocked or partial sweep remains explicitly incomplete.

## 5. Report the result

Deliver the following ledger in the response, or in the requested review artifact. When a PR is requested, put the detailed ledger there and return its link with a concise result. For audit only, label all removal and rewrite sections **Proposed** and distinguish inspection from executed checks. State empty sections as “None” where useful; never imply proposed work was implemented.

1. Open with one short paragraph describing the concrete testing problem, resulting change (or proposals), scope, and optional counts. Link the guidance used and identify exclusions or incomplete areas.
2. Include a tiny before/after visualization only when it clarifies a change, such as a `diff` block contrasting incidental markup with accessible behavior.
3. **Deleted files** — use this table; a row can account for an entire file and should say where useful assertions survive.

   | File | Short justification |
   | --- | --- |
   | `<path>` | `<Specific reason; surviving coverage if applicable.>` |

4. **Removed or consolidated cases in retained files** — group tables by repository area and state common path prefixes. Identify every removed case; group parameterized variants only when their reasons match.

   | File / removed case | Short justification |
   | --- | --- |
   | `<file> — <case>` | `<Pinned detail or duplicate failure; surviving coverage.>` |

5. **Assertions rewritten without removing their scenarios** — short bullets naming the file, brittle assertion, and observable result now checked.
6. A short production-simplification paragraph explaining changes, proposals, or why inspected seams remain justified. Summarize testing-guidance changes or proposals alongside it.
7. **Validation** — compact bullets with actual commands/checks, scope, outcomes, relevant retries, and limitations. A passing slice is not evidence that the full suite passed.

Completion: the report reconciles every removal with a specific justification, distinguishes implemented work from proposals, and states validation and scope limits. Avoid generic justifications such as “low value.”
