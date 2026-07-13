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

## firmware-telemetry/v1 implementation targets

- **Layer 1 implementation** ([firmware-telemetry/v1.md](../firmware-telemetry/v1.md)): the
  contract now defines the signed telemetry envelope, capability manifest,
  provisioning anchor, replay rules, and local-interlock boundary, but
  `ori-edge-firmware`, `ori-runtime`, `ori-verity`, and `ori-gateway` have not
  yet implemented or consumed it.
