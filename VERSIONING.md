# Versioning Policy

## Contract Versions

Contract domains use integer major directories: `v1`, `v2`, `v3`.

A version increment means a breaking change for implementers.

## Current Baseline

| Contract       | Version | Status                                                   |
| -------------- | ------- | -------------------------------------------------------- |
| events         | v1      | Implemented in runtime                                   |
| skills-package | v1      | Implemented in runtime (signature verification gap open) |
| gateway-api    | v1      | Design target                                            |
| cli-commands   | v1      | Derivable from runtime surfaces                          |

## Compatibility

- Additive optional fields are backward-compatible.
- Unknown fields should be ignored by consumers unless explicitly forbidden.
- Breaking changes require a new version directory.

## Deprecation

A deprecated version is retained until all known consumers migrate.
