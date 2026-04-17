# Contributing to ori-specs

## Scope

`ori-specs` is a documentation-only repository that defines versioned contracts for:

- `ori-runtime`
- `ori-gateway`
- `ori-cli`
- `ori-skills`
- `ori-sdk-python`

A contract change can break multiple repositories. Treat every PR as potentially breaking until proven otherwise.

## Change Process

1. Open an issue tagged `contract-change` describing scope and impacted repos.
2. Wait for CODEOWNER acknowledgement.
3. Open PR with:
   - contract file updates
   - `gaps/open.md` or `gaps/resolved.md` updates when relevant
   - migration notes if behavior changes
4. Pass CI validation.
5. Obtain required review approvals.

## Versioning Rules

A new major contract version (`v1` -> `v2`) is required for:

- removing or renaming fields
- changing field type or semantics
- changing required/optional status
- tightening accepted enum values in a way that rejects previously valid payloads

Same-version updates are allowed for:

- additive optional fields
- additive enum values (backward-compatible)
- clarifications that do not change behavior
- marking already-implemented gaps as resolved

## Writing Rules

- Do not describe planned behavior as implemented.
- Every section must label status clearly: `Implemented`, `Design Target`, or `Open Gap`.
- Keep field names and types exactly aligned with source repos.
- If behavior is ambiguous, mark it explicitly in a `Notes` or `Ambiguity` subsection.

## Review Criteria

A contract PR is ready only if:

- runtime behavior and spec text match
- internal links resolve
- no contradictory status statements remain
