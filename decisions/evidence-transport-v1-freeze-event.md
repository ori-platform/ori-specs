# evidence-transport/v1: the freeze event

Decided 2026-10-01.

## Decision

At `1d46303` the gateway README lists, under "Implemented in this repository",
the authority HTTPS channel "defined by `gateway-api/v1` and
`evidence-transport/v1`". That names the contract a channel is defined by. It
does not state that the repository implements or conforms to the version, so
under the claim conditions in [VERSIONING.md](../VERSIONING.md) it is
provenance, and the claiming-merge freeze recorded on it was a
misclassification.

The version stays frozen. Its freeze event is the next valid one: the evidence
authority's release `ingest-v0.1.0` (`43f03e8`, 2026-10-01), which vendors
`evidence-transport/vectors/ingest-auth.json`, declared
`evidence-transport/v1`, and tests its ingest authentication against it. The
freeze revision is `1e94ec0`, the revision that release pins, at which the
version's normative text is identical to `0c34880`.

The history basis is `1e94ec0`. It is the ori-specs state at the 2026-10-01
shipment: the release vendors the version's vector from that revision and pins
it, so that is the ori-specs history the release was built and tested against,
not whatever the default branch held that day. The contract revision is
`06ba18e`, the last commit that changed `evidence-transport/v1.md` in that
history.

## Rederivation

- No qualifying claim: the gateway's only statement is the one above; the
  authority's sources described an adapter "for" the version while its only
  binary refused production posture, and later name `evidence-transport/v2`.
- No gateway release exists.
- The first release that ships an implementation of the version is
  `ingest-v0.1.0`.

The correction is recorded in
[status/evidence-transport/v1.json](../status/evidence-transport/v1.json).
