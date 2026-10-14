# commissioned-safety-binding/v2: the freeze commit

Decided 2026-10-14.

## Decision

The record of `commissioned-safety-binding/v2` names its freezing commit as
runtime `332754b1f0a3`. No runtime commit has that ID. The event it describes is
right: the shipment of runtime release `v2.5.0-rc.11` on 2026-09-23, which
vendors the version's corpus from ori-specs `7d9a6e8` and pins that revision.
That release's tag resolves to runtime commit
`332754bec2bf00f854eecd43783add4a0a5a8bff` ("chore(release): prepare
v2.5.0-rc.11"). The recorded ID shares its first seven characters, `332754b`,
with it and then differs, so it is a transcription of the tag commit's ID.

`frozen_by.commit` is corrected to `332754bec2bf` as a transcription, under
[VERSIONING.md](../VERSIONING.md#corrections). Every other field of
`frozen_by` stands: the event, the repository, the date, the release, the
contract revision and the pinned revision. No event is replaced, so the
correction names no history basis. The runtime adoption entry's
`implementation.commit` carried the same ID and is corrected with it.

## Rederivation

- `git rev-parse v2.5.0-rc.11^{commit}` in the runtime repository gives
  `332754bec2bf00f854eecd43783add4a0a5a8bff`.
- `git cat-file -e 332754b1f0a3^{commit}` there fails: the recorded ID names no
  commit. The hosting service answers the same.
- The release's manifest under
  `tests/vectors/commissioned_safety_binding/` at that commit pins
  `source_commit` `7d9a6e8`, the record's pinned revision.
