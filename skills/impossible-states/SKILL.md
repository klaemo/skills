---
name: impossible-states
description: Simplify a codebase by making impossible states unrepresentable and carrying parsed types through every consumer.
disable-model-invocation: true
---

# Impossible states

Simplify the requested code scope in the typed functional programming tradition: parse external input, preserve the information in its type, and let interior code consume that type. Propose concrete simplifications and follow them through to their fullest conclusion. For an explicitly audit-only request, investigate and report proposals without implementing them.

## 1. Discover repository guidance and scope

Read applicable repository and directory instructions first. Discover coding standards, code style guides, contribution guidance, and architecture or domain decisions by searching both filenames and document contents, including hidden documentation directories. Follow links from repository entry points and instructions; filenames and locations vary. Read the guidance applicable to each area before choosing its simplifications. If none is found, state that and use established local patterns.

Inventory the whole requested scope, including application code, operational scripts, infrastructure, configuration, and tests where present. Identify generated, vendored, migration, or otherwise protected files and their repository-specific handling. Record inspected areas, exclusions, and gaps; an area may need no changes.

Completion: applicable guidance is identified and read, and every area in the requested scope is accounted for in the inspection plan.

## 2. Trace invariants and propose deletions

Trace actual data paths from external input through parsing, domain operations, persistence, transport, and consumers. For each candidate, identify the invariant, the mechanism establishing it, and where that guarantee is lost. Stored invariants need evidence from constraints, controlled write paths, or versioned decoders; a row type alone describes expectations.

Look for:

- Repeated validation of already parsed values and parallel schemas or types.
- Optional-field records and independent flags that admit contradictory states.
- Operation results broader than the operation can actually return.
- Caller-controlled defaults that should belong to the creation operation.
- Production interfaces used only by tests, unused exports, and redundant helpers.

Propose the stronger representation and the downstream machinery it removes. Use the language's idioms: discriminated alternatives, required payloads per state, nonempty structures, schema-derived types, or encapsulated constructors. Use narrowly scoped parser-created brands when a runtime refinement would otherwise be erased; assertions do not establish validity. Prefer changes that remove complexity across consumers over abstractions that merely relocate it.

Completion: each proposal names the establishing boundary or owner, affected consumers, expected deletions, and compatibility concerns. Resolve uncertainty about domain intent before changing that behavior.

## 3. Follow each change through

Implement proposals within the authorized scope. Carry the stronger representation through every caller, response, UI state, fixture, test, import, and export. Keep values that change together in one state; associate asynchronous callbacks with their attempt and preserve rules for stale or terminal callbacks. Let creation operations own invariant defaults and return the narrowest guaranteed result.

Before removing a defense, identify what makes its input impossible. Preserve checks for real possibilities:

- Authorization, concurrent writes, and conditional updates establish current facts, including after external calls.
- Independent storage reads, network responses, and historical payloads remain parsing or compatibility boundaries.
- Provider events can be duplicated, incomplete, or out of order; process and resource ownership can change.

Treat an SDK as a parsing boundary only when its runtime behavior establishes the required guarantees. Inspect serialized contracts and generated schemas when strengthening shared types, including schemas supplied to external models. Preserve external behavior and historical compatibility unless the requested scope includes changing them.

Completion: every affected consumer uses the stronger representation, obsolete machinery is removed, and retained checks have a concrete boundary or mutable-fact justification. A new parser or type alone is unfinished work.

## 4. Verify at the owning layer

Exercise invalid input at the rejecting boundary and construct valid fixtures through real parsers or constructors. Before deleting an impossible-state test, identify the invariant that excludes its input and retain rejection coverage at the boundary. Remove scaffolding made obsolete by the change while preserving tests for distinct observable regressions.

Run repository-prescribed checks and exercise affected user journeys where the environment permits. Report failures, setup repairs, and untested paths explicitly. Distinguish a successful command or capture from evidence of the intended behavior and diagnostic state.

Completion: relevant checks and journeys have results or explicit limitations, and remaining failures are investigated enough to distinguish demonstrated causes from uncertainty.

## 5. Report proposal to result

Use the audit structure below, keeping verification evidence separate from the two-column table. Report incomplete, blocked, or audit-only proposals as such in the Result column. Link the actual repository guidance discovered; if none exists, state that in the opening paragraph.

Deliver the report in the response unless the user or repository specifies a file. Publishing a PR, editing repository guides, and deleting existing reports are separate actions requiring applicable authorization.

```markdown
# Impossible-state audit

This audit covers <owned code areas and configuration>. It applies the
repository's <linked style guidance>: parse external input once, preserve the
information in its type, and let interior code use that type.
<Explicit exclusions and any coverage limitations.>

## Simplifications followed through

| Proposal | Result |
| --- | --- |
| <Concrete simplification in terms of an invariant or ownership.> | <Implemented shape, affected consumers, and redundant machinery removed.> |

## Checks that remain necessary

- **<Boundary or mutable fact>:** <Why the check protects a real possibility
  that the stronger internal type cannot exclude; relevant decision link.>
```

Completion: results distinguish implemented work from proposals, scope gaps are explicit, necessary checks are explained, and verification evidence states its limits.
