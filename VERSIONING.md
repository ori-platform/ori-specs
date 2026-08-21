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
| gateway-api                | v1      | Implemented baseline                                             |
| cli-commands               | v1      | Runtime entrypoint implemented; CLI command set planned          |
| runtime-health             | v2      | v2 implemented in runtime and CLI; `2.4.x` and earlier serve v1 |
| runtime-telemetry          | v1      | Implemented baseline                                             |
| runtime-mobile             | v1      | Implemented baseline                                             |
| device-policy              | v1      | Implemented baseline                                             |
| offline-tokens             | v1      | Implemented baseline                                             |
| runtime-config             | v1      | Implemented baseline                                             |
| runtime-config-signing     | v1      | Implemented baseline                                             |
| runtime-release-bundle     | v1      | Design target                                                    |
| signing                    | v1      | Runtime manifest and SDK profiles implemented; adoption pending  |
| evidence                   | v1      | Implemented baseline; anchoring/ingestion design targets         |
| firmware-telemetry         | v1      | Implemented baseline; HIL pending                                |
| firmware-commands          | v1      | Implemented baseline; HIL pending                                |
| device-provisioning        | v1      | Implemented baseline; HIL pending                                |
| firmware-mqtt-provisioning | v1      | Implemented baseline; HIL pending                                |

## Compatibility

- Additive optional fields are backward-compatible.
- Unknown fields should be ignored by consumers unless explicitly forbidden.
- Breaking changes require a new version directory.

## Deprecation

A deprecated version is retained until all known consumers migrate.
