# ori-specs

Versioned contract definitions for the Ori ecosystem.

This repository is documentation-only. It contains no runtime implementation code.

## Contracts

| Contract | File | Status |
| --- | --- | --- |
| Event schema | [events/v1.md](events/v1.md) | Implemented |
| Skill package format | [skills-package/v2.md](skills-package/v2.md) | Implemented in runtime >= 2.4.0 |
| Skill package format (previous) | [skills-package/v1.md](skills-package/v1.md) | Superseded by v2; accurate for runtime < 2.4.0 |
| Skill hook isolation | [skill-hook-isolation/v1.md](skill-hook-isolation/v1.md) | Design target; community hook execution disabled until implemented |
| Gateway API | [gateway-api/v1.md](gateway-api/v1.md) | Implemented baseline; evidence carriage implemented on both sides, pre-ratification |
| Evidence Transport | [evidence-transport/v1.md](evidence-transport/v1.md) | Design target, pre-ratification; implemented by the evidence authority and the gateway, not deployed |
| Evidence Audit | [evidence-audit/v1.md](evidence-audit/v1.md) | Design target |
| Evidence Commissioning Ingest | [evidence-commissioning-ingest/v1.md](evidence-commissioning-ingest/v1.md) | Design target, pre-ratification; the authority-side write path for signed commissioning authorisations and their revocation |
| CLI commands | [cli-commands/v1.md](cli-commands/v1.md) | Contract target; runtime entrypoint implemented |
| Runtime health socket RPC | [runtime-health/v2.md](runtime-health/v2.md) | v2 implemented in runtime and CLI; `2.4.x` and earlier serve v1 |
| Runtime telemetry HTTP export | [runtime-telemetry/v1.md](runtime-telemetry/v1.md) | Implemented baseline |
| Android runtime mobile payload | [runtime-mobile/v1.md](runtime-mobile/v1.md) | Implemented baseline |
| Android runtime payload publication | [runtime-mobile/v2.md](runtime-mobile/v2.md) | Design target |
| Device policy | [device-policy/v1.md](device-policy/v1.md) | Implemented baseline |
| Offline Tier C tokens | [offline-tokens/v1.md](offline-tokens/v1.md) | Implemented baseline |
| Runtime config surface | [runtime-config/v1.md](runtime-config/v1.md) | Implemented baseline |
| Runtime config surface (closed) | [runtime-config/v2.md](runtime-config/v2.md) | Design target, pre-ratification; the provisioning document closed at every level, with a machine schema |
| Runtime configuration orchestration | [runtime-config-orchestration/v1.md](runtime-config-orchestration/v1.md) | Design target, pre-ratification; how the provisioning, entitlement and commissioning artifacts compose into one accepted set |
| Runtime config signing/install | [runtime-config-signing/v1.md](runtime-config-signing/v1.md) | Implemented baseline |
| Runtime release bundle/install | [runtime-release-bundle/v1.md](runtime-release-bundle/v1.md) | Design target |
| Gateway config surface | [gateway-config/v1.md](gateway-config/v1.md) | Implemented baseline |
| Skill signing contract | [signing/v1.md](signing/v1.md) | Runtime manifest and SDK manifest/artifact profiles implemented; adoption pending |
| Gateway MQTT canonical JSON | [gateway-mqtt-canonical-json/v1.md](gateway-mqtt-canonical-json/v1.md) | Implemented baseline; cross-language vectors for the runtime, the gateway and the evidence authority |
| Runtime evidence anchor | [runtime-evidence-anchor/v1.md](runtime-evidence-anchor/v1.md) | Design target, pre-ratification; derivation implemented in the runtime and the evidence authority, the latter not yet vector-proven |
| Evidence chain | [evidence/v2.md](evidence/v2.md) | Design target, pre-ratification; produced natively by the runtime and verified by the evidence authority |
| Evidence chain (previous) | [evidence/v1.md](evidence/v1.md) | Superseded by v2 for new rows; retained for rows written by the retired private producer; anchor registry and export ingestion superseded by evidence-exchange/v1 |
| Evidence exchange (off-site delivery) | [evidence-exchange/v1.md](evidence-exchange/v1.md) | Design target, pre-ratification; every half implemented on main, no end-to-end proven exchange |
| Firmware telemetry / evidence Layer 1 | [firmware-telemetry/v1.md](firmware-telemetry/v1.md) | Implemented baseline; HIL pending |
| Firmware commands | [firmware-commands/v1.md](firmware-commands/v1.md) | Implemented baseline; HIL pending |
| Device provisioning lifecycle | [device-provisioning/v1.md](device-provisioning/v1.md) | Implemented baseline; HIL pending |
| Firmware MQTT transport-identity provisioning | [firmware-mqtt-provisioning/v1.md](firmware-mqtt-provisioning/v1.md) | Implemented baseline; HIL pending |
| Device configuration | [device-configuration/v1.md](device-configuration/v1.md) | Contract target |
| Commissioned safety binding | [commissioned-safety-binding/v1.md](commissioned-safety-binding/v1.md) | Design target, pre-ratification |
| Safety profile | [safety-profile/v1.md](safety-profile/v1.md) | Design target, pre-ratification; release-owned Tier D conditions activated by commissioned zones |
| Capability grant | [capability-grant/v1.md](capability-grant/v1.md) | Design target, pre-ratification; signed skill authority ceiling, capped at Tier C |
| Capability grant issuance | [capability-grant-issuance/v1.md](capability-grant-issuance/v1.md) | Design target, pre-ratification; issuance, custody, rotation, denial by signed release, implicit Tier A |
| Safety qualification fixture | [safety-qualification-fixture/v1.md](safety-qualification-fixture/v1.md) | Design target, pre-ratification; the bounded, release-signed instrument that exercises one candidate safety profile on one commissioned zone under supervision |
| Safety profile ratification | [safety-profile/ratification.md](safety-profile/ratification.md) | Design target, pre-ratification; the six arguments a dossier must close before a profile's status may flip, and who decides |
| Qualification session procedure | [safety-qualification-fixture/procedure.md](safety-qualification-fixture/procedure.md) | Design target, pre-ratification; how a supervised session is conducted, and the source of the fixture exposure ceilings |
| Hazard session procedure | [safety-qualification-fixture/hazard-session.md](safety-qualification-fixture/hazard-session.md) | Design target, pre-ratification and unreviewed; discrimination and protection runs for the trip-point argument |

## Gaps

- Open gaps: [gaps/open.md](gaps/open.md)
- Resolved gaps: [gaps/resolved.md](gaps/resolved.md)

## Governance

- Contribution policy: [CONTRIBUTING.md](CONTRIBUTING.md)
- Versioning policy: [VERSIONING.md](VERSIONING.md)
- Security policy: [SECURITY.md](SECURITY.md)
