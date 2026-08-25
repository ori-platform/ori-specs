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
| Gateway API | [gateway-api/v1.md](gateway-api/v1.md) | Implemented baseline; evidence carriage pending |
| Evidence Transport | [evidence-transport/v1.md](evidence-transport/v1.md) | Design target |
| Evidence Audit | [evidence-audit/v1.md](evidence-audit/v1.md) | Design target |
| CLI commands | [cli-commands/v1.md](cli-commands/v1.md) | Contract target; runtime entrypoint implemented |
| Runtime health socket RPC | [runtime-health/v2.md](runtime-health/v2.md) | v2 implemented in runtime and CLI; `2.4.x` and earlier serve v1 |
| Runtime telemetry HTTP export | [runtime-telemetry/v1.md](runtime-telemetry/v1.md) | Implemented baseline |
| Android runtime mobile payload | [runtime-mobile/v1.md](runtime-mobile/v1.md) | Implemented baseline |
| Device policy | [device-policy/v1.md](device-policy/v1.md) | Implemented baseline |
| Offline Tier C tokens | [offline-tokens/v1.md](offline-tokens/v1.md) | Implemented baseline |
| Runtime config surface | [runtime-config/v1.md](runtime-config/v1.md) | Implemented baseline |
| Runtime config signing/install | [runtime-config-signing/v1.md](runtime-config-signing/v1.md) | Implemented baseline |
| Runtime release bundle/install | [runtime-release-bundle/v1.md](runtime-release-bundle/v1.md) | Design target |
| Gateway config surface | [gateway-config/v1.md](gateway-config/v1.md) | Implemented baseline |
| Skill signing contract | [signing/v1.md](signing/v1.md) | Runtime manifest and SDK manifest/artifact profiles implemented; adoption pending |
| Gateway MQTT canonical JSON | [gateway-mqtt-canonical-json/v1.md](gateway-mqtt-canonical-json/v1.md) | Implemented baseline; cross-language vectors for runtime, gateway and Verity |
| Runtime evidence anchor | [runtime-evidence-anchor/v1.md](runtime-evidence-anchor/v1.md) | Design target; derivation of the identifiers a runtime seals evidence under |
| Evidence chain | [evidence/v2.md](evidence/v2.md) | Design target; neutral vocabulary and the first public byte-level specification |
| Evidence chain (previous) | [evidence/v1.md](evidence/v1.md) | Implemented baseline; format specified only in the private producer; anchor registry and export ingestion superseded by evidence-exchange/v1 |
| Evidence exchange (off-site delivery) | [evidence-exchange/v1.md](evidence-exchange/v1.md) | Design target |
| Firmware telemetry / evidence Layer 1 | [firmware-telemetry/v1.md](firmware-telemetry/v1.md) | Implemented baseline; HIL pending |
| Firmware commands | [firmware-commands/v1.md](firmware-commands/v1.md) | Implemented baseline; HIL pending |
| Device provisioning lifecycle | [device-provisioning/v1.md](device-provisioning/v1.md) | Implemented baseline; HIL pending |
| Firmware MQTT transport-identity provisioning | [firmware-mqtt-provisioning/v1.md](firmware-mqtt-provisioning/v1.md) | Implemented baseline; HIL pending |
| Device configuration | [device-configuration/v1.md](device-configuration/v1.md) | Contract target |

## Gaps

- Open gaps: [gaps/open.md](gaps/open.md)
- Resolved gaps: [gaps/resolved.md](gaps/resolved.md)

## Governance

- Contribution policy: [CONTRIBUTING.md](CONTRIBUTING.md)
- Versioning policy: [VERSIONING.md](VERSIONING.md)
- Security policy: [SECURITY.md](SECURITY.md)
