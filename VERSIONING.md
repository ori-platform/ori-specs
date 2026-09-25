# Versioning Policy

## Contract Versions

Contract domains use integer major directories: `v1`, `v2`, `v3`.

A version increment means a breaking change for implementers.

## Status Records

Each contract version has exactly one status record,
`status/<contract>/<version>.json`, and that record is the single source of
truth for the version's status. The `Status:` paragraph at the top of the
contract, its row in [README.md](README.md) and its row in the baseline table
below are summaries of the record, and `scripts/check-contract-status` refuses
any that disagrees with it.

A record carries:

| Field | Meaning |
| --- | --- |
| `contract`, `version` | The version it describes; they match the record's path. |
| `maturity` | `draft` or `ratified`. |
| `ratified_by` | Only when `ratified`: the path, under `decisions/`, and optional `#anchor`, of the decision record in this repository that ratified the version. |
| `adoption` | One entry per implementation, by role. Each names `role`, `repository`, `lifecycle`, `conformance`, and the `claim`, `implementation` and optional `current` snapshots described below. |
| `compatibility` | `amendable` or `frozen`. |
| `frozen_by` | Only when `frozen`: the `event` that froze the version, its `repository`, `commit`, `date` and `release`; `contract_revision`, the last ori-specs commit that changed the contract before the event; `pinned_revision`, the ori-specs commit the freezing artifact pins where it pins one; `branch_protection`, `conservative`, and a `reason` for a conservative baseline. |
| `baseline_audit` | Only when `frozen`, and always then: the audit of the version's text against its freeze revision. See [Baseline Audit](#baseline-audit). |
| `proof` | `end_to_end`, `hil` and `production`, each with its own `state` of `none`, `partial` or `established`, and its own evidence. |
| `notes` | Optional prose. |

Adoption vocabularies:

- `role`: `producer`, `consumer`, `gateway`, `authority`, `verifier`,
  `issuer`, `cli`, `firmware`, `runtime`.
- `repository`: `runtime`, `gateway`, `evidence-authority`, `cli`,
  `edge-firmware`, `sdk-python`, `skills-hub`.
- `lifecycle`: `unimplemented`; `implemented-unreleased`, on the repository's
  default branch and in no release; `shipped`, included in a released tag,
  named in `implementation.release`.
- `conformance`: `unassessed`, `conforming`, `partial` or `non-conforming`.
  Anything other than `unassessed` needs a behavioural assessment, recorded
  separately as `assessment`: the `snapshot` it assessed (`implementation` or
  `current`) and that snapshot's `commit`, the `method`, and `assessed_at`. It
  compares the implementation's behaviour with the reconstructed baseline. A
  vendored revision, path, vector pin, reference or claim is provenance only,
  and on its own gives `unassessed`. `partial` names its
  `assessment.missing_requirements[]`; `non-conforming` names each of its
  `assessment.divergences[]`; an implementation that is both incomplete and
  contradictory is `non-conforming` and records both.

Each entry records three snapshots, and never pairs a value with a commit at
which it did not exist:

- `claim`: `{status}`, where `status` is `claimed` or `none`. A `claimed`
  entry adds `commit`, the commit carrying the qualifying claim, and
  `claim_citation`, which existed at that commit. A citation is one declaration,
  `{path_base, component, path, line, text}`, with `text` the qualifying
  sentence quoted exactly, or one heading-plus-item span, `{path_base,
  component, path, heading_line, heading_text, item_lines, item_text}`, for a
  list item that qualifies only under its heading. Nothing else stands in for
  a heading. A contract naming an implementation as its source of truth is
  not a conformance claim by that implementation.
- `implementation`: the `commit` of the live implementation (for a shipped
  entry with no qualifying claim, the commit of its first release), its
  `release`, the `paths` that implement it with their `path_base` and
  `component`, and the `vendored_revision` its vector manifest pins at that
  same commit.
- `current` (optional): the current implementation `commit` and the
  `vendored_revision` its manifest pins there.

A conformance assessment is its own record and never attaches to the claim
commit.

`vendored_revision` is input-document provenance only. It is not a conformance
claim and not a conformance result, and a vendored revision that differs from
the freeze revision proves nothing either way.

Citation paths are relative to the base declared by `path_base`:

- `repository-root` — the default for public repositories;
- `component-root` — used when a repository contains a separately rooted
  implementation component or when exposing the repository layout would
  disclose a private repository name.

When `path_base` is `component-root`, `component` MUST contain a stable logical
identifier such as `library-crate`, not a private repository name or local
filesystem path.

A citation MUST identify its implementation repository role, commit, path
base, path, and exact text or line span. The repository role may be inherited
from the containing adoption entry rather than duplicated inside
`claim_citation`.

Absolute paths and paths containing `..` are forbidden. The combination of
repository role, commit, path base, component where applicable, and path MUST
resolve to exactly one source location in the audit environment.

Existing citations remain `repository-root`; they do not require migration
merely because the evidence authority uses `component-root`.

`scripts/check-contract-status` checks this structurally, and only for the
forms it lists: relative paths, no `..`, no absolute or local paths, and a
component that is a logical identifier. The check is not complete. It does not
detect private names, and known forms pass it: `file:///`, `https://`, `$HOME/`
and `%2e%2e/` paths, and any path written into a free-text field such as
`notes` or `assessment.method`.

`baseline_audit.issue` is the only issue reference a status record, a
generated status paragraph or row, `README.md`, `VERSIONING.md` or
`CONTRIBUTING.md` may carry. The checker matches the listed reference forms
only and is not complete: a reference in backticks or emphasis, `:#N`,
`pull/N`, and pull-request URLs pass it.

These combinations are refused: `unimplemented` with `claimed`;
`unimplemented` with any assessed conformance; `conforming` with divergences;
`partial` with an unqualified full claim; `non-conforming` without a
divergence; `shipped` without a release identifier.

`frozen_by.branch_protection` is `protected` or `unprotected`, as the hosting
service reported the repository's default branch when the record was written.
It is provenance evidence and never a condition of the freeze.

## Status Model

A version's status is three independent axes and three separate proof facts.
None of them implies another.

- **Maturity** says whether the text is settled. `ratified` cites the decision
  that ratified it; without such a record the version is `draft`.
- **Adoption** says who implements the version, per role. One implemented role
  says nothing about the others: a shipped producer is not a working exchange.
- **Compatibility** says whether the text may still change in place. It is
  derived from the freeze rule below, not chosen.
- **Proof** records end-to-end, hardware-in-the-loop and production evidence,
  each separately. Implementation and shipment are never proof: a record
  cannot establish a proof field by citing a release or a commit.

Some words are descriptive only and carry no status:

- *Design target* describes intent. It is not a maturity state, and it is not
  used of a version with a shipped implementation.
- *Pre-ratification* means `maturity: draft` and nothing else. It says nothing
  about implementation, shipment or compatibility.
- *Implemented* never means ratified, and is not used of a version whose every
  adoption entry is `partial` or `unimplemented`.
- *Shipped* never means end-to-end, HIL or operationally proven.

Ratification of a safety profile under
[safety-profile/ratification.md](safety-profile/ratification.md) is a fact
about a profile in `profiles.json`. It is separate from the `maturity` of
`safety-profile/v1`.

## The Freeze Rule

A contract freezes when a conformance-claiming implementation is merged to
the repository's default branch, whether or not the hosting service reports
that branch as protected. Protection or equivalent merge governance is
recorded as provenance evidence, not as a precondition for freezing.

Feature branches, experiments, unused helpers, mocks, test-only work and
explicitly partial or non-conforming implementations don't freeze a contract.
Shipment freezes it independently, and is stronger evidence of external
reliance. A contract freezes at version granularity, never section
granularity. Ratification is independent of all of this: implementation or
shipment never implies ratification.

A freeze therefore comes from one of three events, recorded in
`frozen_by.event`:

- `claiming-merge`: an explicit conformance claim (a capability matrix,
  README, release note or manifest, or a qualifying source-file claim as
  defined below) merged to a default branch with the implementation it
  describes;
- `shipment`: a released artifact that implements the version;
- `conservative-baseline`: a reviewed determination recording the exact code
  revision (`repository`, `commit`), the exact `contract_revision` and a stated
  `reason`, with `conservative: true`. A contract naming an implementation as
  its source of truth is not a claim; where such a version must still be held,
  it is held by this determination. `conservative-baseline` is reserved for
  cases with no explicit qualifying claim, and is never used to obtain an
  earlier freeze than a qualifying claim or a shipment gives. A conservative
  baseline is timed to the contract's publication, and records the code
  revision current at that time.

A source-file conformance claim freezes a contract only when all of these
hold: 1. It names the exact contract and version. 2. It expressly states that
the repository, component or identified implementation role implements or
conforms to that version. 3. It is merged to the default branch with the
production implementation it describes. 4. It is not qualified as planned,
partial, experimental, test-only, illustrative or non-conforming. 5. The
status record cites the exact repository commit, file and line or declaration
containing the claim. A source reference that merely says code builds, parses,
signs, verifies, transports, tests, or uses an object "per" a contract is
provenance, not a conformance claim. A test fixture naming a contract is also
not a claim. Whether the assertion was correct is assessed separately. A
mistaken conformance claim still freezes the version and does not unfreeze
when corrected or removed.

A qualifying claiming merge must contain, or coexist on the default branch
with, the production implementation described by the claim. For an executable
service, runtime, CLI or firmware behaviour, that implementation must be
reachable from a supported production entry point or workflow. For a library
or SDK contract, an exported public API included in the product artifact may
qualify without an executable entry point. For installer or release contracts,
the implementation must be invoked by the real install or release workflow.
Unused types, unwired helpers, tests, fixtures and illustrative
implementations do not qualify. The freeze falls at the first default-branch
state where the claim and the live implementation coexist.

A qualifying conformance claim merged with its production implementation
freezes the contract immediately, including when the implementation's
conformance is still unassessed. A later assessment of partial or
non-conforming behaviour does not move or revoke the freeze event. Shipment
independently freezes the contract, whether the shipped implementation claimed
conformance, was unassessed, or was later found non-conforming.

Once frozen, a breaking or semantic change requires a new contract version.
The only changes admitted in place are those listed under
[Compatibility](#compatibility), each classified in the version's baseline
audit.

A version never unfreezes. Removing a release, abandoning an implementation or
reverting the claim leaves it frozen.

A version whose text changed in place after its freezing event has each
change classified in its baseline audit as an erratum, additive, or semantic;
a semantic change moves to a successor version. The earlier status label being
stale is not a reason to keep a semantic change in place.

## Compatibility

A frozen version admits exactly two kinds of change in place:

- **Additive**: a new field, member, enumeration value or message that is
  optional to produce, where a consumer that ignores unknown fields keeps
  conforming and a consumer that does not know it rejects nothing it
  previously accepted. A field a producer must emit, a value a consumer must
  refuse, or a tightened rule is not additive.
- **Erratum**: An erratum may correct spelling, formatting, a broken
  reference, or text whose correction cannot change any conforming producer
  output, accepted input, state transition, rejection result, ordering,
  authority, or consumer interpretation. An erratum MUST NOT be used to make
  an implementation conform retroactively. Any observable or normative
  correction requires a successor version.

Consumers ignore unknown fields unless the contract explicitly forbids them.
Where a contract forbids unknown fields, adding one is a breaking change for
that contract.

Everything else, including removing or renaming a field, changing a type or
meaning, changing whether a field is required, and tightening what is
accepted, needs a new version directory.

A `draft`, `amendable` version may change in any way. Its record says it is
amendable, so that the freedom is a stated fact rather than an assumption a
later change inherits.

## Baseline Audit

Every frozen record carries `baseline_audit`. Its compatibility stays `frozen`
whatever the audit's state; an audit is never a compatibility state, and a
pending audit is never shown as `amendable`.

| Field | Meaning |
| --- | --- |
| `status` | `pending` or `complete`. |
| `freeze_revision` | `frozen_by.pinned_revision` where the freezing artifact pins one, otherwise `frozen_by.contract_revision`. It is the oracle for drift, not `main`. |
| `changes_since_freeze` | The ori-specs commits after `freeze_revision` that changed the contract or a non-Markdown file in its directory. |
| `issue` | The URL of this repository's issue tracking the audit, or `null`. |
| `pinned_sha256` | While `pending`: the SHA-256 of the contract text with its `Status:` paragraph removed (`--digest` prints it). |
| `pinned_files` | While `pending`: `{path: sha256}` for every file of the version other than its `v<N>.md`, vectors and companion documents alike (`--digest` prints them). A file belongs to the version its name carries; a companion document to the version whose record lists it in `companion_files`; a file under a directory in the checker's attribution table to that directory's owner; and any other non-Markdown file to the only version of a single-version directory. |
| `contract_sha256` | When `complete`: the SHA-256 of the contract file at `freeze_revision`. |
| `vectors` | When `complete`: `{path: sha256}` for each file of the version other than its `v<N>.md`, vectors and companion documents alike, at `freeze_revision`. The set of files attributed to the version must stay equal to it. |
| `role_commits` | When `complete`: `{role, repository, commit}` for every adoption entry. |
| `hunks` | When `complete`: every difference from `freeze_revision`, each with `hunk_sha256`, `classification` (`erratum`, `additive` or `semantic`), a `summary`, the introducing `commit` where known, `destination` (the successor version) for a semantic hunk, and `optional: true` and `unknown_field_compatible: true` for an additive one. |
| `appended_files` | When `complete`, optional: each file added to the version after its freeze, with `path`, its `sha256`, `classification` (only `additive` is admitted), a `justification` of why no conforming consumer's accepted input changes, the `clause` it exercises quoted exactly, and `clause_basis`: `freeze_revision`, the clause is in the contract at `freeze_revision`, or `additive_hunk`, it is in the recorded additive hunk named by `hunk_sha256`. A new negative vector for a frozen version cites a `freeze_revision` clause and introduces no new reason, precedence, vocabulary, canonicalisation or strictness. |

While an audit is **pending**, the version takes no normative edit.
`scripts/check-contract-status` refuses any change to the contract text other
than its `Status:` paragraph, and any change to the version's pinned files;
the paragraph and the status record stay editable. A pending audit with no
tracking issue is reported as a gap, and a gap fails the check until an issue
is recorded.

The checker also compares each record with its version at the merge base with
the default branch, and refuses:

- a frozen record made amendable or deleted;
- while the audit is pending, a change to `frozen_by`, `freeze_revision`,
  `pinned_sha256` or `pinned_files`;
- once the audit is complete, a return to `pending`, a change to
  `frozen_by`, `freeze_revision`, `contract_sha256` or `vectors`, or any change
  to a recorded hunk (hunks may only be appended);
- a shipped or claimed adoption entry removed or moved to an earlier
  lifecycle, a claim changed from `claimed` to `none`, an assessed conformance
  downgraded to `unassessed`, a recorded divergence erased, and a shipped
  release rewritten.

A record that is new in the change has no merge-base version and cannot be
compared.

When an audit is **complete**, the checker reads `freeze_revision` from
history, confirms the recorded contract and vector hashes, and compares the
current text with the baseline. Every difference must be a recorded
`erratum` or `additive` hunk; an unrecorded difference is drift, a recorded
`semantic` hunk must not remain, and its destination must exist. Frozen vector
files must still match their recorded hashes. An appended file must be absent at
`freeze_revision`, match its recorded hash, and quote a clause that exists where
its basis says; the check cannot tell whether the file is really additive, and
review does. `--draft-audit
<contract>/<version>` prints the hashes and net hunks for a reviewer to
classify.

The checker verifies hashes and presence. It cannot verify that a hunk
classified additive is additive, or that an erratum meets the definition
above; review does.

### Pre-ratification exception

Retired. It allowed a version labelled *Design Target* to change in place,
including semantically, until a shipped consumer claimed conformance. The
freeze rule replaces it and freezes a version earlier, at the first
conformance-claiming merge to a default branch. Contract text that cites the
exception records the reasoning of changes made while it applied.

## Companion Documents

A document in a contract directory other than `v<N>.md`, such as
`safety-profile/ratification.md`, has no status record of its own. It shares
its version's maturity, and a change to it that alters a frozen version's
semantics needs a new version like any other.

A companion document is attributed to one version: by a version named in its
file name, by the checker's attribution table for its directory, or by the
`companion_files` list of the version's status record where neither applies.
An unattributed companion document in a frozen contract's directory is
refused. An attributed one is pinned with the version's other files, so a
pending audit refuses any edit, addition, removal or re-attribution, and a
complete audit refuses the same against its recorded baseline.

## Current Baseline

Validated against `status/`; the records carry adoption and proof in full.

| Contract | Version | Maturity | Compatibility | Frozen by |
| --- | --- | --- | --- | --- |
| capability-grant | v1 | `draft` | `amendable` | — |
| capability-grant-issuance | v1 | `draft` | `amendable` | — |
| cli-commands | v1 | `draft` | `frozen` | shipment, runtime `eee40d3`, 2026-05-08; audit complete |
| cli-commands | v2 | `draft` | `amendable` | — |
| commissioned-safety-binding | v1 | `draft` | `frozen` | shipment, runtime `5d14895`, 2026-09-07; audit pending |
| device-configuration | v1 | `draft` | `amendable` | — |
| device-policy | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit pending |
| device-provisioning | v1 | `draft` | `frozen` | claiming-merge, runtime `2ed10df`, 2026-07-21; audit pending |
| events | v1 | `draft` | `frozen` | shipment, runtime `eee40d3`, 2026-05-08; audit pending |
| evidence | v1 | `draft` | `frozen` | shipment, runtime `ee242ed`, 2026-07-24; audit pending |
| evidence | v2 | `draft` | `frozen` | shipment, runtime `5a82b87`, 2026-08-25; audit complete |
| evidence | v3 | `draft` | `amendable` | — |
| evidence-audit | v1 | `draft` | `amendable` | — |
| evidence-commissioning-ingest | v1 | `draft` | `amendable` | — |
| evidence-exchange | v1 | `draft` | `frozen` | claiming-merge, gateway `1d46303`, 2026-08-25; audit complete |
| evidence-exchange | v2 | `draft` | `amendable` | — |
| evidence-transport | v1 | `draft` | `frozen` | claiming-merge, gateway `1d46303`, 2026-08-25; audit complete |
| evidence-transport | v2 | `draft` | `amendable` | — |
| firmware-commands | v1 | `draft` | `frozen` | claiming-merge, runtime `95a08b7`, 2026-07-17; audit pending |
| firmware-mqtt-provisioning | v1 | `draft` | `frozen` | claiming-merge, runtime `92e8620`, 2026-07-23; audit pending |
| firmware-telemetry | v1 | `draft` | `frozen` | claiming-merge, runtime `8aefc63`, 2026-07-16; audit pending |
| gateway-api | v1 | `draft` | `frozen` | claiming-merge, gateway `663c99f`, 2026-06-04; audit complete |
| gateway-api | v2 | `draft` | `amendable` | — |
| gateway-config | v1 | `draft` | `frozen` | conservative-baseline, gateway `a2e462d`, 2026-06-09; audit complete |
| gateway-config | v2 | `draft` | `amendable` | — |
| gateway-mqtt-canonical-json | v1 | `draft` | `frozen` | shipment, runtime `5a82b87`, 2026-08-25; audit pending |
| offline-tokens | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit complete |
| operator-socket | v1 | `draft` | `amendable` | — |
| runtime-config | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit pending |
| runtime-config | v2 | `draft` | `amendable` | — |
| runtime-config-orchestration | v1 | `draft` | `amendable` | — |
| runtime-config-signing | v1 | `draft` | `frozen` | shipment, runtime `aa970be`, 2026-07-10; audit pending |
| runtime-evidence-anchor | v1 | `draft` | `frozen` | claiming-merge, runtime `b5ab285`, 2026-08-23; audit complete |
| runtime-evidence-anchor | v2 | `draft` | `amendable` | — |
| runtime-health | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit pending |
| runtime-health | v2 | `draft` | `frozen` | shipment, runtime `5a82b87`, 2026-08-25; audit complete |
| runtime-health | v3 | `draft` | `amendable` | — |
| runtime-mobile | v1 | `draft` | `frozen` | shipment, runtime `aa970be`, 2026-07-10; audit pending |
| runtime-mobile | v2 | `draft` | `frozen` | claiming-merge, runtime `1f7fe6c`, 2026-09-16; audit pending |
| runtime-release-bundle | v1 | `draft` | `frozen` | shipment, runtime `cad2279`, 2026-08-13; audit pending |
| runtime-telemetry | v1 | `draft` | `frozen` | shipment, runtime `aa970be`, 2026-07-10; audit pending |
| runtime-telemetry | v2 | `draft` | `frozen` | shipment, runtime `f87be13`, 2026-09-16; audit pending |
| safety-profile | v1 | `draft` | `frozen` | shipment, runtime `5d14895`, 2026-09-07; audit pending |
| safety-qualification-fixture | v1 | `draft` | `amendable` | — |
| schema-descriptor | v1 | `draft` | `frozen` | shipment, runtime `c90f94d`, 2026-08-29; audit pending |
| sensor-configuration | v1 | `draft` | `frozen` | shipment, runtime `c90f94d`, 2026-08-29; audit pending |
| signing | v1 | `draft` | `frozen` | shipment, runtime `1411814`, 2026-06-12; audit complete |
| signing | v2 | `draft` | `amendable` | — |
| skill-hook-isolation | v1 | `draft` | `amendable` | — |
| skills-package | v1 | `draft` | `frozen` | shipment, runtime `eee40d3`, 2026-05-08; audit pending |
| skills-package | v2 | `draft` | `frozen` | shipment, runtime `1b4c659`, 2026-08-17; audit pending |
| skills-package | v3 | `draft` | `amendable` | — |
| supply-transfer | v1 | `draft` | `amendable` | — |

## Deprecation

A deprecated version is retained until all known consumers migrate.
