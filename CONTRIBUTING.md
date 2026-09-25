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

The freeze rule, the status model and the changes a frozen version admits are
defined once, in [VERSIONING.md](VERSIONING.md). Read them there rather than
restating them, so the two documents cannot drift. In short:

- A version is frozen from the first conformance-claiming implementation
  merged to a repository's default branch, protected or not, or from
  shipment, whichever comes first, and never unfreezes.
- A frozen version admits only additive optional changes and errata as
  VERSIONING defines them, each classified in its status record's
  `baseline_audit`; anything else needs a new version directory. A frozen
  version whose audit is pending takes no normative edit at all.
- A draft version whose record says `amendable` may change in any way.

`status/<contract>/<version>.json` is the source of truth for a version's
maturity, adoption, compatibility and proof. A PR that adds a version, lands an
implementation, cuts a release or ratifies a contract updates the record, then
runs `scripts/check-contract-status --write-status-lines --write-rows` to
re-render the contract's `Status:` paragraph and its README and VERSIONING
rows.

## Writing Rules

- Do not describe planned behavior as implemented.
- A contract's `Status:` paragraph is rendered from its record; do not edit it
  by hand.
- Section-level labels such as `Implemented`, `Design Target` or `Open Gap`
  describe that section only and never contradict the record. *Design target*
  and *pre-ratification* carry no status of their own; see
  [VERSIONING.md](VERSIONING.md#status-model).
- Keep field names and types exactly aligned with source repos.
- If behavior is ambiguous, mark it explicitly in a `Notes` or `Ambiguity` subsection.

## Review Criteria

A contract PR is ready only if:

- runtime behavior and spec text match
- internal links resolve
- no contradictory status statements remain
- `scripts/check-contract-status` passes
