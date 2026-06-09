# Open Gaps

## G-15 — Tier C enrichment MQTT transport wiring

- Domain: `gateway-api/v1`
- Status: Contract defined in `ori-gateway` (`internal/contracts/tier_c_enrichment.go`), DECISIONS.md entry recorded. MQTT topic constants and `app.go` handler are not yet wired. Runtime has no client for sending enrichment requests.
- Summary: The advisory Tier C enrichment flow requires topic constants in `contracts.go` and a subscription handler in `app.go` on the gateway side, and a corresponding MQTT client on the runtime side. Until wired, enriched Tier C operator messages are unavailable.
- Tracking: `ori-platform/ori-gateway#41`

## G-16 — `reasoning_log` export missing from gateway runtimeclient

- Domain: `gateway-api/v1`
- Status: Export type documented in spec and served by runtime. Gateway `runtimeclient/mqtt.go` implements `Health`, `SensorHistory`, `ActionLog`, `TierCDecisionLog` but has no `ReasoningLog` method.
- Summary: The reasoning audit export surface is unusable from the gateway until the runtimeclient method is added.
- Tracking: `ori-platform/ori-gateway#42`
