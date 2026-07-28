# Open Gaps

New implementation gaps should be added here with repo tracking links.

## evidence/v1 design targets

- **Anchor registry** ([evidence/v1.md](../evidence/v1.md)): no off-device
  store binds `device_id` to `initial_pubkey_hex`; the anchor is only logged at
  first provisioning. Owner/store undecided (likely ori-cloud or a site-local
  signed file; the gateway has no persistent store).
- **Evidence export ingestion** ([evidence/v1.md](../evidence/v1.md)): chain
  rows are marked `exported` locally but no authenticated receiver exists;
  signed receipts are the design target.
## firmware-telemetry/v1 proof targets

- **Layer 1 real-device proof** ([firmware-telemetry/v1.md](../firmware-telemetry/v1.md)):
  the producer (`ori-edge-firmware`) and the consumer (`ori-runtime`) implement
  and cite this contract — the signed telemetry envelope, capability manifest,
  heartbeat, `(boot_id, seq)` replay defence, signed fault events, and the
  local-interlock boundary — against shared cross-language golden vectors, and
  the anchor lifecycle and cross-store confirmation surface it resolves against
  are implemented in the runtime and the private evidence-store implementation.
  What remains is proof rather than definition: the live factory-fresh ESP32-S3
  provisioning and broker path, tracked in `ori-edge-firmware` #12 and #14.

## firmware-telemetry/v1 implementation targets

- **`storage_degraded` producer adoption**
  ([firmware-telemetry/v1.md](../firmware-telemetry/v1.md)): the code and its
  late-reporting semantics are defined here, and receiver acceptance is
  provided by `ori-runtime` #270, which must precede any firmware rollout.
  **No producer emits the code.** `ori-edge-firmware` currently discards the
  result of appending a signed envelope to its offline buffer, so a
  measurement can consume a `(boot_id, seq)` pair and then vanish with no
  durable record.

  The remaining device-side work is a latch held outside the failed store,
  cleared only once the resulting fault is itself durably stored, with
  emission after recovery or on the next boot — the fault publisher writes to
  the same buffer whose failure it would report. A shared golden fault vector
  follows that implementation and removes the single-code exception asserted
  in `ori-runtime`'s fault-vector coverage test. Tracked in
  `ori-edge-firmware` #62.

  Note the receiver cannot substitute freshness analysis for this code: `seq`
  comes from a reservation ceiling, so a reboot legitimately skips a range and
  gaps are expected rather than diagnostic.

## firmware-mqtt-provisioning/v1 implementation targets

- **Signed transport-identity provisioning**
  ([firmware-mqtt-provisioning/v1.md](../firmware-mqtt-provisioning/v1.md)):
  the sealed firmware key/certificate lifecycle, provisioning-authority signer,
  shared vectors, bounded device verifier, and concrete delivery adapter are
  implemented and host-tested. What remains is the hardware-gated
  factory-fresh provisioning and broker-path proof tracked in
  `ori-edge-firmware` #12, #14, and #48.

## signing/v1 implementation targets

- **Community skill artifact verification**
  ([signing/v1.md](../signing/v1.md)): the runtime implements the embedded
  strict canonical-manifest verifier and pins the shared vectors. The SDK
  implements the separate canonical-manifest and detached exact-artifact
  profiles against those vectors. Remaining end-to-end adoption is author and
  Hub artifact signing in the publish pipeline (`ori-skills-hub` #4 and #9)
  and pre-extraction install verification (`ori-cli` #10). Implementations
  must keep artifact and manifest verification as separate entry points.
