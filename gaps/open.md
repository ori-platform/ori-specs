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
- **Public `protocol_version` projection**
  ([evidence/v1.md](../evidence/v1.md),
  [runtime-health/v1.md](../runtime-health/v1.md)): the health
  `protocol_version` field is defined to carry the public evidence contract
  version `evidence.v1`, but the runtime still surfaces the loaded artifact's
  raw declared identifier. Aligning it while keeping private-artifact validation
  internal is tracked in `ori-runtime` #252.

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

## firmware-mqtt-provisioning/v1 implementation targets

- **Signed transport-identity provisioning**
  ([firmware-mqtt-provisioning/v1.md](../firmware-mqtt-provisioning/v1.md)):
  the sealed firmware key/certificate lifecycle primitives exist, but the
  provisioning-authority signer, shared vectors, bounded device verifier,
  concrete USB/network adapter, and HIL proof are not yet implemented.
