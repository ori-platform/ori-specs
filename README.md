# ori-specs

Versioned contract definitions for the Ori ecosystem.

This repository is documentation-only. It contains no runtime implementation code.

## Contracts

Each contract version's status lives in its record under [`status/`](status/),
and [VERSIONING.md](VERSIONING.md#status-records) defines the fields. The rows
below are checked against those records.

| Contract | File | Maturity | Adoption | Compatibility |
| --- | --- | --- | --- | --- |
| Capability grant | [capability-grant/v1.md](capability-grant/v1.md) | `draft` | none recorded | `amendable` |
| Capability grant issuance | [capability-grant-issuance/v1.md](capability-grant-issuance/v1.md) | `draft` | none recorded | `amendable` |
| CLI commands | [cli-commands/v1.md](cli-commands/v1.md) | `draft` | runtime `shipped` in v0.9.0-beta.1, `partial` at assessed implementation snapshot `eee40d3` (7 missing requirements); current `d2aba48` unassessed, cli `implemented-unreleased`, `unassessed` | `frozen` (2026-05-08), audit `complete` |
| CLI commands | [cli-commands/v2.md](cli-commands/v2.md) | `draft` | none recorded | `amendable` |
| Operator Socket | [operator-socket/v1.md](operator-socket/v1.md) | `draft` | none recorded | `amendable` |
| Commissioned safety binding | [commissioned-safety-binding/v1.md](commissioned-safety-binding/v1.md) | `draft` | producer (cli) `implemented-unreleased`, `unassessed`, verifier (cli) `implemented-unreleased`, `unassessed`, consumer (runtime) `shipped` in v2.5.0-rc.8, `unassessed` | `frozen` (2026-09-07), audit `pending` |
| Device configuration | [device-configuration/v1.md](device-configuration/v1.md) | `draft` | none recorded | `amendable` |
| Device policy | [device-policy/v1.md](device-policy/v1.md) | `draft` | consumer (runtime) `shipped` in v0.9.0-beta.2, `unassessed` | `frozen` (2026-05-11), audit `pending` |
| Device provisioning lifecycle | [device-provisioning/v1.md](device-provisioning/v1.md) | `draft` | consumer (runtime) `shipped` in v2.1.0, claimed, `unassessed`, consumer (evidence-authority) `implemented-unreleased`, `unassessed`, producer (edge-firmware) `implemented-unreleased`, `unassessed` | `frozen` (2026-07-21), audit `pending` |
| Event schema | [events/v1.md](events/v1.md) | `draft` | producer (runtime) `shipped` in v0.9.0-beta.1, `unassessed` | `frozen` (2026-05-08), audit `pending` |
| Evidence chain (previous) | [evidence/v1.md](evidence/v1.md) | `draft` | runtime `shipped` in v2.1.0, `unassessed`, producer (evidence-authority) `implemented-unreleased`, `unassessed` | `frozen` (2026-07-24), audit `pending` |
| Evidence chain | [evidence/v2.md](evidence/v2.md) | `draft` | producer (runtime) `shipped` in v2.5.0-rc.3, `unassessed`, verifier (evidence-authority) `implemented-unreleased`, `unassessed` | `frozen` (2026-08-25), audit `complete` |
| Evidence chain | [evidence/v3.md](evidence/v3.md) | `draft` | none recorded | `amendable` |
| Evidence Audit | [evidence-audit/v1.md](evidence-audit/v1.md) | `draft` | none recorded | `amendable` |
| Evidence Commissioning Ingest | [evidence-commissioning-ingest/v1.md](evidence-commissioning-ingest/v1.md) | `draft` | authority (evidence-authority) `implemented-unreleased`, `unassessed` | `amendable` |
| Evidence exchange (off-site delivery) | [evidence-exchange/v1.md](evidence-exchange/v1.md) | `draft` | producer (runtime) `shipped` in v2.5.0-rc.3, `unassessed`, consumer (runtime) `shipped` in v2.5.0-rc.3, `unassessed`, gateway `implemented-unreleased`, claimed, `unassessed`, authority (evidence-authority) `implemented-unreleased`, `unassessed` | `frozen` (2026-08-25), audit `complete` |
| Evidence exchange (off-site delivery) | [evidence-exchange/v2.md](evidence-exchange/v2.md) | `draft` | none recorded | `amendable` |
| Evidence Transport | [evidence-transport/v1.md](evidence-transport/v1.md) | `draft` | authority (evidence-authority) `implemented-unreleased`, `non-conforming` at assessed current snapshot `146a462` (1 divergence), gateway `implemented-unreleased`, claimed, `unassessed` | `frozen` (2026-08-25), audit `complete` |
| Evidence Transport | [evidence-transport/v2.md](evidence-transport/v2.md) | `draft` | none recorded | `amendable` |
| Firmware commands | [firmware-commands/v1.md](firmware-commands/v1.md) | `draft` | producer (runtime) `shipped` in v2.1.0, claimed, `unassessed`, consumer (edge-firmware) `implemented-unreleased`, `unassessed` | `frozen` (2026-07-17), audit `pending` |
| Firmware MQTT transport-identity provisioning | [firmware-mqtt-provisioning/v1.md](firmware-mqtt-provisioning/v1.md) | `draft` | issuer (runtime) `shipped` in v2.1.0, claimed, `unassessed`, consumer (edge-firmware) `implemented-unreleased`, claimed, `unassessed` | `frozen` (2026-07-23), audit `pending` |
| Firmware telemetry / evidence Layer 1 | [firmware-telemetry/v1.md](firmware-telemetry/v1.md) | `draft` | producer (edge-firmware) `implemented-unreleased`, claimed, `unassessed`, verifier (runtime) `shipped` in v2.1.0, claimed, `unassessed`, verifier (sdk-python) `implemented-unreleased`, `unassessed`, verifier (evidence-authority) `implemented-unreleased`, `unassessed` | `frozen` (2026-07-16), audit `pending` |
| Gateway API | [gateway-api/v1.md](gateway-api/v1.md) | `draft` | runtime `shipped` in v1.0.0, `unassessed`, gateway `implemented-unreleased`, claimed, `unassessed`, consumer (sdk-python) `implemented-unreleased`, `unassessed` | `frozen` (2026-06-04), audit `complete` |
| Gateway API | [gateway-api/v2.md](gateway-api/v2.md) | `draft` | none recorded | `amendable` |
| Gateway config surface | [gateway-config/v1.md](gateway-config/v1.md) | `draft` | gateway `implemented-unreleased`, `non-conforming` at assessed current snapshot `4c28c93` (1 divergence) | `frozen` (2026-06-09), audit `complete` |
| Gateway config surface | [gateway-config/v2.md](gateway-config/v2.md) | `draft` | none recorded | `amendable` |
| Gateway MQTT canonical JSON | [gateway-mqtt-canonical-json/v1.md](gateway-mqtt-canonical-json/v1.md) | `draft` | runtime `shipped` in v2.5.0-rc.3, `unassessed`, gateway `implemented-unreleased`, `unassessed` | `frozen` (2026-08-25), audit `pending` |
| Offline Tier C tokens | [offline-tokens/v1.md](offline-tokens/v1.md) | `draft` | verifier (runtime) `shipped` in v0.9.0-beta.2, `unassessed`, cli `implemented-unreleased`, `unassessed` | `frozen` (2026-05-11), audit `complete` |
| Runtime config surface | [runtime-config/v1.md](runtime-config/v1.md) | `draft` | consumer (runtime) `shipped` in v0.9.0-beta.2, `unassessed` | `frozen` (2026-05-11), audit `pending` |
| Runtime config surface (closed) | [runtime-config/v2.md](runtime-config/v2.md) | `draft` | none recorded | `amendable` |
| Runtime configuration orchestration | [runtime-config-orchestration/v1.md](runtime-config-orchestration/v1.md) | `draft` | none recorded | `amendable` |
| Runtime config signing/install | [runtime-config-signing/v1.md](runtime-config-signing/v1.md) | `draft` | consumer (runtime) `shipped` in v2.0.0, `unassessed` | `frozen` (2026-07-10), audit `pending` |
| Runtime evidence anchor | [runtime-evidence-anchor/v1.md](runtime-evidence-anchor/v1.md) | `draft` | producer (runtime) `shipped` in v2.5.0-rc.3, claimed, `unassessed`, verifier (evidence-authority) `implemented-unreleased`, `unassessed` | `frozen` (2026-08-23), audit `complete` |
| Runtime health socket RPC (previous) | [runtime-health/v1.md](runtime-health/v1.md) | `draft` | producer (runtime) `shipped` in v0.9.0-beta.2, `unassessed`, consumer (sdk-python) `implemented-unreleased`, `unassessed`, cli `implemented-unreleased`, `unassessed` | `frozen` (2026-05-11), audit `pending` |
| Runtime health socket RPC | [runtime-health/v2.md](runtime-health/v2.md) | `draft` | producer (runtime) `shipped` in v2.5.0-rc.3, `non-conforming` at assessed current snapshot `d2aba48` (2 divergences), cli `implemented-unreleased`, `unassessed` | `frozen` (2026-08-25), audit `complete` |
| Runtime health socket RPC | [runtime-health/v3.md](runtime-health/v3.md) | `draft` | none recorded | `amendable` |
| Android runtime mobile payload | [runtime-mobile/v1.md](runtime-mobile/v1.md) | `draft` | producer (runtime) `shipped` in v2.0.0, `unassessed` | `frozen` (2026-07-10), audit `pending` |
| Android runtime payload publication | [runtime-mobile/v2.md](runtime-mobile/v2.md) | `draft` | producer (runtime) `shipped` in v2.5.0-rc.10, claimed, `unassessed`, verifier (runtime) `shipped` in v2.5.0-rc.10, claimed, `unassessed` | `frozen` (2026-09-16), audit `pending` |
| Runtime release bundle/install | [runtime-release-bundle/v1.md](runtime-release-bundle/v1.md) | `draft` | producer (runtime) `shipped` in v2.3.0, `unassessed`, consumer (runtime) `shipped` in v2.3.0, `unassessed` | `frozen` (2026-08-13), audit `pending` |
| Runtime telemetry HTTP export | [runtime-telemetry/v1.md](runtime-telemetry/v1.md) | `draft` | producer (runtime) `shipped` in v2.0.0, `unassessed` | `frozen` (2026-07-10), audit `pending` |
| Runtime telemetry HTTP export | [runtime-telemetry/v2.md](runtime-telemetry/v2.md) | `draft` | producer (runtime) `shipped` in v2.5.0-rc.10, `unassessed` | `frozen` (2026-09-16), audit `pending` |
| Safety profile | [safety-profile/v1.md](safety-profile/v1.md) | `draft` | consumer (runtime) `shipped` in v2.5.0-rc.8, `unassessed` | `frozen` (2026-09-07), audit `pending` |
| Safety qualification fixture | [safety-qualification-fixture/v1.md](safety-qualification-fixture/v1.md) | `draft` | none recorded | `amendable` |
| Schema descriptor core | [schema-descriptor/v1.md](schema-descriptor/v1.md) | `draft` | consumer (runtime) `shipped` in v2.5.0-rc.6, `unassessed` | `frozen` (2026-08-29), audit `pending` |
| Sensor configuration | [sensor-configuration/v1.md](sensor-configuration/v1.md) | `draft` | consumer (runtime) `shipped` in v2.5.0-rc.6, `unassessed` | `frozen` (2026-08-29), audit `pending` |
| Skill signing contract | [signing/v1.md](signing/v1.md) | `draft` | verifier (runtime) `shipped` in v1.0.0, `non-conforming` at assessed implementation snapshot `1411814` (1 divergence); current `d2aba48` unassessed, issuer (skills-hub) `implemented-unreleased`, `unassessed`, verifier (sdk-python) `implemented-unreleased`, `unassessed` | `frozen` (2026-06-12), audit `complete` |
| Skill signing contract | [signing/v2.md](signing/v2.md) | `draft` | none recorded | `amendable` |
| Skill hook isolation | [skill-hook-isolation/v1.md](skill-hook-isolation/v1.md) | `draft` | none recorded | `amendable` |
| Skill package format (previous) | [skills-package/v1.md](skills-package/v1.md) | `draft` | consumer (runtime) `shipped` in v0.9.0-beta.1, `unassessed`, producer (sdk-python) `implemented-unreleased`, `unassessed`, consumer (skills-hub) `implemented-unreleased`, `unassessed` | `frozen` (2026-05-08), audit `pending` |
| Skill package format | [skills-package/v2.md](skills-package/v2.md) | `draft` | consumer (runtime) `shipped` in v2.4.0-rc.3, `unassessed` | `frozen` (2026-08-17), audit `pending` |
| Skill package format (next) | [skills-package/v3.md](skills-package/v3.md) | `draft` | verifier (evidence-authority) `implemented-unreleased`, `unassessed` | `amendable` |
| Supply transfer | [supply-transfer/v1.md](supply-transfer/v1.md) | `draft` | none recorded | `amendable` |

Companion documents carry no record of their own; see
[VERSIONING.md](VERSIONING.md#companion-documents):

- [safety-profile/ratification.md](safety-profile/ratification.md): the six
  arguments a dossier must close before a profile's status may flip, and who
  decides.
- [safety-qualification-fixture/procedure.md](safety-qualification-fixture/procedure.md):
  how a supervised session is conducted, and the source of the fixture
  exposure ceilings.
- [safety-qualification-fixture/hazard-session.md](safety-qualification-fixture/hazard-session.md):
  discrimination and protection runs for the trip-point argument; unreviewed.

## Gaps

- Open gaps: [gaps/open.md](gaps/open.md)
- Resolved gaps: [gaps/resolved.md](gaps/resolved.md)

## Governance

- Contribution policy: [CONTRIBUTING.md](CONTRIBUTING.md)
- Versioning policy: [VERSIONING.md](VERSIONING.md)
- Security policy: [SECURITY.md](SECURITY.md)
