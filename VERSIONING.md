# Versioning Policy

## Contract Versions

Contract domains use integer major directories: `v1`, `v2`, `v3`.

A version increment means a breaking change for implementers.

## Current Baseline

| Contract                   | Version | Status                                                           |
| -------------------------- | ------- | ---------------------------------------------------------------- |
| events                     | v1      | Implemented in runtime                                           |
| skills-package             | v2      | Implemented in runtime >= 2.4.0; v1 superseded, accurate < 2.4.0 |
| skill-hook-isolation       | v1      | Design target; not implemented                                   |
| gateway-api                | v1      | Implemented baseline; outbound evidence carriage is a pre-ratification design target |
| evidence-transport         | v1      | Design target, pre-ratification; no deployed conforming service |
| evidence-audit             | v1      | Design target, pre-ratification; no deployed conforming service |
| cli-commands               | v1      | Runtime entrypoint implemented; CLI command set planned          |
| runtime-health             | v2      | v2 implemented in runtime and CLI; `2.4.x` and earlier serve v1 |
| runtime-telemetry          | v1      | Implemented baseline                                             |
| runtime-mobile             | v1      | Implemented baseline                                             |
| device-policy              | v1      | Implemented baseline                                             |
| offline-tokens             | v1      | Implemented baseline                                             |
| runtime-config             | v2      | v2 design target, pre-ratification; v1 implemented baseline      |
| runtime-config-signing     | v1      | Implemented baseline                                             |
| runtime-release-bundle     | v1      | Design target                                                    |
| signing                    | v1      | Runtime manifest and SDK profiles implemented; adoption pending  |
| evidence                   | v2      | v2 design target, neutral vocabulary and public byte format; v1 is what the private producer emits |
| evidence-exchange          | v1      | Design target, pre-ratification; no shipped conforming consumer |
| firmware-telemetry         | v1      | Implemented baseline; HIL pending                                |
| firmware-commands          | v1      | Implemented baseline; HIL pending                                |
| device-provisioning        | v1      | Implemented baseline; HIL pending                                |
| firmware-mqtt-provisioning | v1      | Implemented baseline; HIL pending                                |
| commissioned-safety-binding | v1    | Design target, pre-ratification; no shipped conforming consumer |

## Compatibility

- Additive optional fields are backward-compatible.
- Unknown fields should be ignored by consumers unless explicitly forbidden.
- Breaking changes require a new version directory.

### Pre-ratification exception

A contract whose status is **Design Target** MAY be amended in place, including
in ways that break semantics, until it has a shipped consumer claiming
conformance. Such a contract has no deployed implementation to break, and
issuing a new major version for it would publish a compatibility generation
that never existed.

**Once any implementation ships claiming conformance, the exception is spent**
and the ordinary rule applies: a breaking semantic change requires a new
version directory. Conformance tests in a repository that has not shipped the
behaviour do not spend it; a released artifact whose documentation or capability
matrix claims the contract does.

A contract amended under this exception MUST record in the baseline table above
that it is pre-ratification and has no shipped conforming consumer, so the
exception's availability is a stated fact rather than an assumption a later
change can quietly inherit.

## Deprecation

A deprecated version is retained until all known consumers migrate.
