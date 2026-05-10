# Open Gaps

## G-04 — Gateway transport not implemented end-to-end

- Domain: `gateway-api/v1`
- Status: Open
- Summary: Tier 3 request/response transport is not wired in runtime production path yet.

## G-05 — Gateway availability check not broker-heartbeat based

- Domain: `gateway-api/v1`
- Status: Open
- Summary: Runtime now tracks `ori/gateway/health` heartbeat in capability posture, but full Tier 3 gateway reasoning path is not wired end-to-end yet.

## G-12 — ContextEnricher device snapshot integration (post-v1 scope)

- Domain: `skills-package/v1`, `runtime-config/v1`
- Status: Deferred (post-v1.0.0)
- Summary: Prompt-time cross-sensor device snapshot fusion is approved architecture but intentionally deferred until single-device v1.0.0 stability is confirmed in PoC deployments.
