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
| `adoption` | One entry per implementation, by role. Each names `role`, `repository`, `lifecycle`, `conformance`, and the `claim`, `implementation` and optional `current` snapshots described below, with an optional `assessment` and `prior_assessments`; see [Assessment History](#assessment-history). |
| `compatibility` | `amendable` or `frozen`. |
| `frozen_by` | Only when `frozen`: the `event` that froze the version, its `repository`, `commit`, `date` and `release`; `contract_revision`, the last ori-specs commit that changed the contract before the event; `pinned_revision`, the ori-specs commit the freezing artifact pins where it pins one; `branch_protection`, `conservative`, and a `reason` for a conservative baseline. |
| `baseline_audit` | Only when `frozen`, and always then: the audit of the version's text against its freeze revision. See [Baseline Audit](#baseline-audit). |
| `proof` | `end_to_end`, `hil` and `production`, each with its own `state` of `none`, `partial` or `established`, and its own evidence. |
| `notes` | Optional prose. |
| `corrections` | Optional, append-only: each recorded correction of a misclassified freeze, audit or claim. See [Corrections](#corrections). |

Adoption vocabularies:

- `role`: `producer`, `consumer`, `gateway`, `authority`, `verifier`,
  `issuer`, `cli`, `firmware`, `runtime`, `installer`.
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

### Assessment History

An entry may keep the assessments its `assessment` replaced. It is opt-in: an
entry without `prior_assessments` needs no migration. Once an entry has been
assessed, though, its assessment is never edited in place: correcting even a
typo is a supersession that keeps the earlier text, since `corrections`
cannot reach an assessment.

- `prior_assessments[]` is append-only. Each item is a superseded
  `assessment` kept whole, with the `conformance` it carried. When the active
  `assessment` changes, the change appends the one it replaces, unaltered,
  and nothing else. No item is ever edited, reordered or removed, an entry
  that has been assessed is never removed, and an entry or record new in a
  change carries no `prior_assessments`: history is what the default branch
  already recorded. Assessments are dated in order, oldest first, then the
  active one. Each implementation has one entry: no two entries of a record
  share a `role` and `repository`.
- Every divergence and every missing requirement any superseded assessment
  recorded that the active one does not keep appears exactly once in
  `assessment.resolved[]`, as `{divergence | requirement, by, commit |
  contract_revision, evidence}`, quoted exactly, with `evidence` saying what
  shows it resolved. The list is cumulative: a later assessment names again
  what an earlier one resolved.
  - `by: "fix"` names the fixing `commit` in the implementation's
    repository. It descends from every commit at which an assessment
    recorded the item, and is in the history of the commit the active
    assessment assessed. So no fix resolves an item at a commit that
    recorded it.
  - `by: "amended-rule"` names `contract_revision`: the commit of this
    repository the active assessment was judged under. It changed the
    normative text of the version's document, not only its Status block or
    wrapping, and descends from the commit at which this repository's record
    first carried the item. A frozen version's rules do not change, so it
    admits no amended-rule resolution.
- Any snapshot may be reassessed, an earlier one or the same one, with a
  `method` that says why. Such a reassessment can only add divergences: a fix
  cannot descend from a snapshot and also be in an earlier one, so a
  rollback keeps every divergence until a later fix resolves it.
- Superseding an assessment changes no proof level: a change that appends to
  `prior_assessments` leaves `proof` exactly as it was on the default branch,
  neither raised nor lowered. A proof change, a downgrade included, is a
  change of its own.

`scripts/check-contract-status` checks the shape and the amended rule's
commit on every run, and checks the history against the merge base with the
default branch. `--verify-resolutions` checks each fix's ancestry, in CI, by
fetching the public repositories a fix names. A fix in a private repository
is not fetched, and the status line shows it as "(N resolutions unverified by
CI)", for as long as the active assessment names it; `--verify-siblings`
checks it against a local checkout. The status line also counts an entry's
prior assessments.

Stated limits:
- A cherry-picked hotfix that does not descend from the commits that recorded
  an item cannot resolve it by descent. It is recorded as a new assessment of
  a snapshot that contains it, and the item stays until a fix that descends
  is in an assessed snapshot.
- The checker cannot tell whether an amended rule really resolves the item
  it names, or whether a fix fixes it; review does.
- An item recorded by mistake leaves only at a later snapshot or by an
  amended rule. A resolution naming a fix that fixed nothing misstates the
  record; review refuses it.

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

A frozen vector file admits one kind of change, and only in place: the
**metadata erratum**, one or more of which may be recorded in sequence, each
correcting only the descriptive top-level members `vector_set`,
`contract` and `canonicalisation`, each a string before and after, while every
other member, case, input, expected result, signature and ordering stays
structurally identical. It is recorded in the audit's `vector_errata` with the
old and new hashes, the introducing commit, the members it changed, a
justification and the repositories that must re-vendor the corrected bytes. This
is not general permission to alter a frozen vector: a change to any other
member, or to a case, a result or their order, needs a successor version.

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
| `changes_since_freeze` | The ori-specs commits in the checked-out history after `freeze_revision` that changed the contract or a file attributed to the version, except a commit whose only change to them is the version's generated `Status:` paragraph: it touches no attributed file but the contract, the contract less that paragraph is byte-identical before and after, the commit changes the version's own status record, and the paragraph is the one rendered from that record as the commit leaves it. A commit that also changes one normative word, a vector or another attributed file, or whose paragraph is not rendered from this version's record, is listed. `scripts/check-contract-status` derives the list from history and refuses a record whose list differs. |
| `issue` | The URL of this repository's issue tracking the audit, or `null`. |
| `pinned_sha256` | While `pending`: the SHA-256 of the contract text with its `Status:` paragraph removed (`--digest` prints it). |
| `pinned_files` | While `pending`: `{path: sha256}` for every file of the version other than its `v<N>.md`, vectors and companion documents alike (`--digest` prints them). A file belongs to the version its name carries; a companion document to the version whose record lists it in `companion_files`; a file under a directory in the checker's attribution table to that directory's owner; and any other non-Markdown file to the only version of a single-version directory. |
| `contract_sha256` | When `complete`: the SHA-256 of the contract file at `freeze_revision`. |
| `baseline_path` | Optional, and admitted only when the version's own `v<N>.md` does not exist at `freeze_revision`: the path its text had there, as for a successor cut from its predecessor's text after the freeze. It names another version document of the same contract. The baseline, `contract_sha256`, every net hunk, and a correction's derivation of the contract revision in its history basis are then read from that path. |
| `baseline_files` | Optional: `{current path: path at freeze_revision}` for each file in `vectors` absent at the freeze revision under its current path, whose bytes had the earlier name there. Each recorded hash is checked against the earlier path, the erratum guard reads a link to the current name as present at the freeze, and `--verify-siblings` accepts a manifest pin of the earlier name only where the manifest declares the digest recorded in `vectors`; it reads the declaration, not the vendored copy. |
| `vectors` | When `complete`: `{path: sha256}` for each file of the version other than its `v<N>.md`, vectors and companion documents alike, at `freeze_revision`. The set of files attributed to the version must stay equal to it. |
| `role_commits` | When `complete`: `{role, repository, commit}` for every adoption entry. |
| `hunks` | When `complete`: every difference from `freeze_revision`, each with `hunk_sha256`, `classification` (`erratum`, `additive` or `semantic`), a `summary`, the introducing `commit` where known, `destination` (the successor version) for a semantic hunk, and `optional: true` and `unknown_field_compatible: true` for an additive one. |
| `vector_errata` | When `complete`, optional: each metadata erratum to a file in `vectors`, in order, with `path`, `classification` (only `erratum` is admitted), `from_sha256` (the recorded bytes, or the previous erratum's result), `to_sha256`, the introducing `commit`, the changed `members` (each one of `vector_set`, `contract`, `canonicalisation`), a `justification` of why no conforming producer output or accepted input changes, and `revendor`, the repositories that must re-vendor the corrected bytes. |
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
  `frozen_by`, `freeze_revision`, `contract_sha256`, `vectors`, `baseline_path`
  or `baseline_files`, or any change
  to a recorded hunk or vector erratum (both may only be appended);
- a shipped or claimed adoption entry removed or moved to an earlier
  lifecycle, a claim changed from `claimed` to `none`, an assessed conformance
  downgraded to `unassessed`, a recorded divergence erased, and a shipped
  release rewritten.

A record that is new in the change has no merge-base version and cannot be
compared.

### Corrections

A freeze event, an audit or a claim recorded on a misreading of these rules,
such as provenance wording classified as a conformance claim, is corrected only
by appending to the record's `corrections` an entry `{date, decision,
decision_sha256, evidence, old, new}`. `old` and `new` name the same fields,
with their exact values before and after, from a closed set: `frozen_by`, the
audit's `freeze_revision`, `changes_since_freeze`, `pinned_sha256`,
`pinned_files`, `contract_sha256`, `vectors`, `role_commits` and `hunks`, and
an adoption entry's `claim`, which may only become `{"status": "none"}`.
`decision` names a decision record under `decisions/`, `decision_sha256` the
sha256 of its bytes, checked on every run, and `evidence` states the
rederivation. A correction naming `frozen_by` also names
`history_basis_revision`, the ori-specs commit that was the repository's state
at the event, which the decision record names and justifies.

Against the merge base, the checker requires every `old` value to be the
merge-base value and every `new` value to be the record's, and lifts the
refusals above for the named fields only. A replacement freeze event must name
commits in this repository's history, its freeze revision must be its pinned
revision (or its contract revision where nothing pins) and in the history
basis, its contract revision must be the last commit that changed the contract
in that basis, and an adoption entry must record the event. A correction never unfreezes, never
replaces a shipment event, never removes a divergence, and is never changed or
removed once recorded. The checker cannot verify a sibling repository's commit
or release; `--verify-siblings` does. A shipment attributed to the wrong
release or repository is not a misclassification and cannot be corrected this
way; it needs a later, separately ratified change to this schema.

A **transcription** is the one other correction of `frozen_by`, recorded with
`class: "transcription"`. It corrects `frozen_by.commit` alone, where the
recorded ID names no commit and the event's release tag resolves to the
corrected one: a tag named literally, which git accepts as `refs/tags/<release>` and which holds no revision expression (`~`, `^`, `:`, `@{`, `..`), not a branch, a ref or a commit given as the release. `old` and `new` name `frozen_by` only and differ in `commit`
and nothing else; the event has a `release`; no `history_basis_revision` is
named, since no event is replaced; the decision record names the recorded ID,
the corrected commit and the release tag; and an adoption entry of the
freezing repository records the corrected commit at that release. The checker
enforces these conditions. That the recorded ID names no commit and the tag
resolves to the corrected one are in the sibling repository, so
`--verify-siblings` checks them and CI cannot. A transcription may correct a
shipment, because it replaces no event. The bar on a wrong release or
repository stands.

When an audit is **complete**, the checker reads `freeze_revision` from
history, confirms the recorded contract and vector hashes, and compares the
current text with the baseline. Every difference must be a recorded
`erratum` or `additive` hunk; an unrecorded difference is drift, a recorded
`semantic` hunk must not remain, and its destination must exist. Frozen vector
files must still match their recorded hashes, or be reached from them by recorded
metadata errata: each erratum's commit must change the file from its
`from_sha256` to its `to_sha256` and only in its declared members, the chain
must end at the file's current bytes, every erratum and hunk commit must be in
the checked-out history (a squash or rewrite that drops one is refused), every
file compared must parse with no member named twice, and the file must differ from its freeze
revision in nothing but declared descriptive members, compared as parsed JSON
with member order, list order and value types kept. An appended file must be absent at
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
| commissioned-safety-binding | v1 | `draft` | `frozen` | shipment, runtime `5d14895`, 2026-09-07; audit complete |
| commissioned-safety-binding | v2 | `draft` | `frozen` | shipment, runtime `332754b`, 2026-09-23; audit complete |
| device-configuration | v1 | `draft` | `amendable` | — |
| device-policy | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit complete |
| device-policy | v2 | `draft` | `frozen` | shipment, runtime `ee242ed`, 2026-07-24; audit complete |
| device-provisioning | v1 | `draft` | `frozen` | claiming-merge, runtime `2ed10df`, 2026-07-21; audit pending |
| ed25519-key-admission | v1 | `draft` | `amendable` | — |
| ed25519-verification | v1 | `draft` | `amendable` | — |
| events | v1 | `draft` | `frozen` | shipment, runtime `eee40d3`, 2026-05-08; audit pending |
| events | v2 | `draft` | `amendable` | — |
| evidence | v1 | `draft` | `frozen` | shipment, runtime `ee242ed`, 2026-07-24; audit pending |
| evidence | v2 | `draft` | `frozen` | shipment, runtime `5a82b87`, 2026-08-25; audit complete |
| evidence | v3 | `draft` | `amendable` | — |
| evidence-audit | v1 | `draft` | `amendable` | — |
| evidence-commissioning-ingest | v1 | `draft` | `amendable` | — |
| evidence-exchange | v1 | `draft` | `frozen` | claiming-merge, gateway `1d46303`, 2026-08-25; audit complete |
| evidence-exchange | v2 | `draft` | `frozen` | shipment, evidence-authority `43f03e8`, 2026-10-01; audit complete |
| evidence-exchange | v3 | `draft` | `amendable` | — |
| evidence-transport | v1 | `draft` | `frozen` | shipment, evidence-authority `43f03e8`, 2026-10-01; audit complete |
| evidence-transport | v2 | `draft` | `frozen` | shipment, evidence-authority `43f03e8`, 2026-10-01; audit complete |
| firmware-commands | v1 | `draft` | `frozen` | claiming-merge, runtime `95a08b7`, 2026-07-17; audit complete |
| firmware-commands | v2 | `draft` | `amendable` | — |
| firmware-command-result | v1 | `draft` | `amendable` | — |
| firmware-mqtt-provisioning | v1 | `draft` | `frozen` | claiming-merge, runtime `92e8620`, 2026-07-23; audit complete |
| firmware-telemetry | v1 | `draft` | `frozen` | claiming-merge, runtime `8aefc63`, 2026-07-16; audit complete |
| firmware-telemetry | v2 | `draft` | `amendable` | — |
| gateway-api | v1 | `draft` | `frozen` | claiming-merge, gateway `663c99f`, 2026-06-04; audit complete |
| gateway-api | v2 | `draft` | `amendable` | — |
| gateway-config | v1 | `draft` | `frozen` | conservative-baseline, gateway `a2e462d`, 2026-06-09; audit complete |
| gateway-config | v2 | `draft` | `amendable` | — |
| gateway-evidence-carriage | v1 | `draft` | `amendable` | — |
| gateway-mqtt-canonical-json | v1 | `draft` | `frozen` | shipment, runtime `5a82b87`, 2026-08-25; audit pending |
| lan-app-api | v1 | `draft` | `amendable` | — |
| offline-tokens | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit complete |
| offline-tokens | v2 | `draft` | `frozen` | claiming-merge, runtime `31ef157`, 2026-09-26; audit complete |
| operator-socket | v1 | `draft` | `frozen` | claiming-merge, runtime `4078762`, 2026-09-29; audit complete |
| operator-socket | v2 | `draft` | `frozen` | shipment, runtime `25e0d9c`, 2026-10-01; audit complete |
| operator-socket | v3 | `draft` | `amendable` | — |
| runtime-config | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit pending |
| runtime-config | v2 | `draft` | `amendable` | — |
| runtime-config-orchestration | v1 | `draft` | `amendable` | — |
| runtime-config-signing | v1 | `draft` | `frozen` | shipment, runtime `aa970be`, 2026-07-10; audit pending |
| runtime-evidence-anchor | v1 | `draft` | `frozen` | claiming-merge, runtime `b5ab285`, 2026-08-23; audit complete |
| runtime-evidence-anchor | v2 | `draft` | `frozen` | claiming-merge, runtime `01d614a`, 2026-09-26; audit complete |
| runtime-health | v1 | `draft` | `frozen` | shipment, runtime `30bdcda`, 2026-05-11; audit pending |
| runtime-health | v2 | `draft` | `frozen` | shipment, runtime `5a82b87`, 2026-08-25; audit complete |
| runtime-health | v3 | `draft` | `frozen` | shipment, runtime `25e0d9c`, 2026-10-01; audit complete |
| runtime-health | v4 | `draft` | `amendable` | — |
| runtime-mobile | v1 | `draft` | `frozen` | shipment, runtime `aa970be`, 2026-07-10; audit pending |
| runtime-mobile | v2 | `draft` | `frozen` | claiming-merge, runtime `1f7fe6c`, 2026-09-16; audit complete |
| runtime-posture | v1 | `draft` | `amendable` | — |
| runtime-release-bundle | v1 | `draft` | `frozen` | shipment, runtime `cad2279`, 2026-08-13; audit pending |
| runtime-telemetry | v1 | `draft` | `frozen` | shipment, runtime `aa970be`, 2026-07-10; audit pending |
| runtime-telemetry | v2 | `draft` | `frozen` | shipment, runtime `f87be13`, 2026-09-16; audit pending |
| safety-profile | v1 | `draft` | `frozen` | shipment, runtime `5d14895`, 2026-09-07; audit pending |
| safety-qualification-fixture | v1 | `draft` | `amendable` | — |
| schema-descriptor | v1 | `draft` | `frozen` | shipment, runtime `c90f94d`, 2026-08-29; audit complete |
| sensor-configuration | v1 | `draft` | `frozen` | shipment, runtime `c90f94d`, 2026-08-29; audit pending |
| signing | v1 | `draft` | `frozen` | shipment, runtime `1411814`, 2026-06-12; audit complete |
| signing | v2 | `draft` | `amendable` | — |
| skill-hook-isolation | v1 | `draft` | `amendable` | — |
| skills-package | v1 | `draft` | `frozen` | shipment, runtime `eee40d3`, 2026-05-08; audit pending |
| skills-package | v2 | `draft` | `frozen` | shipment, runtime `1b4c659`, 2026-08-17; audit pending |
| skills-package | v3 | `draft` | `amendable` | — |
| supply-transfer | v1 | `draft` | `amendable` | — |
| tier-c-approval | v1 | `draft` | `frozen` | shipment, runtime `25e0d9c`, 2026-10-01; audit pending |
| tier-c-approvers | v1 | `draft` | `amendable` | — |

## Deprecation

A deprecated version is retained until all known consumers migrate.
