# Versioning Policy

## Contract Versions

Contract domains use integer major directories: `v1`, `v2`, `v3`.

A version increment means a breaking change for implementers.

## Current Baseline

| Contract       | Version | Status                                                   |
| -------------- | ------- | -------------------------------------------------------- |
| events         | v1      | Implemented in runtime                                   |
| skills-package | v1      | Implemented in runtime                                   |
| gateway-api    | v1      | Implemented baseline                                     |
| cli-commands   | v1      | Runtime entrypoint implemented; CLI command set planned  |
| runtime-health | v1      | Implemented                                              |
| runtime-telemetry | v1    | Implemented baseline                                     |
| runtime-mobile | v1      | Implemented baseline                                     |
| device-policy  | v1      | Implemented baseline                                     |
| offline-tokens | v1      | Implemented baseline                                     |
| runtime-config | v1      | Implemented baseline                                     |
| runtime-config-signing | v1 | Implemented baseline                             |
| signing        | v1      | Implemented baseline                                     |
| evidence       | v1      | Implemented baseline; anchoring/ingestion design targets |

## Compatibility

- Additive optional fields are backward-compatible.
- Unknown fields should be ignored by consumers unless explicitly forbidden.
- Breaking changes require a new version directory.

## Deprecation

A deprecated version is retained until all known consumers migrate.
