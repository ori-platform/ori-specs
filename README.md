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
| Gateway API | [gateway-api/v1.md](gateway-api/v1.md) | Implemented baseline |
| CLI commands | [cli-commands/v1.md](cli-commands/v1.md) | Contract target; runtime entrypoint implemented |
| Runtime health socket RPC | [runtime-health/v1.md](runtime-health/v1.md) | Implemented |
| Runtime telemetry HTTP export | [runtime-telemetry/v1.md](runtime-telemetry/v1.md) | Implemented baseline |
| Android runtime mobile payload | [runtime-mobile/v1.md](runtime-mobile/v1.md) | Implemented baseline |
| Device policy | [device-policy/v1.md](device-policy/v1.md) | Implemented baseline |
| Offline Tier C tokens | [offline-tokens/v1.md](offline-tokens/v1.md) | Implemented baseline |
| Runtime config surface | [runtime-config/v1.md](runtime-config/v1.md) | Implemented baseline |
| Runtime config signing/install | [runtime-config-signing/v1.md](runtime-config-signing/v1.md) | Implemented baseline |
| Runtime release bundle/install | [runtime-release-bundle/v1.md](runtime-release-bundle/v1.md) | Design target |
| Gateway config surface | [gateway-config/v1.md](gateway-config/v1.md) | Implemented baseline |
| Skill signing contract | [signing/v1.md](signing/v1.md) | Runtime manifest and SDK manifest/artifact profiles implemented; adoption pending |
| Evidence chain | [evidence/v1.md](evidence/v1.md) | Implemented baseline; anchor registry and export ingestion superseded by evidence-exchange/v1 |
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
