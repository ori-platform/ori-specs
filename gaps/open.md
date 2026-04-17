# Open Gaps

## G-04 — Gateway transport not implemented end-to-end

- Domain: `gateway-api/v1`
- Status: Open
- Summary: Tier 3 request/response transport is not wired in runtime production path yet.

## G-05 — Gateway availability check not broker-heartbeat based

- Domain: `gateway-api/v1`
- Status: Open
- Summary: Runtime tier availability still needs gateway-heartbeat authoritative check.

## G-07 — Skill signature verification not enforced

- Domain: `skills-package/v1`
- Status: Open
- Summary: `signature` field exists in skill YAML but runtime load-time cryptographic verification is pending.

## G-11 — Runtime health/status RPC socket absent

- Domain: `cli-commands/v1`
- Status: Open
- Summary: CLI cannot query live structured runtime health via socket yet.
