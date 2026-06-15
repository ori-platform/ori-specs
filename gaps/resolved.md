# Resolved Gaps

## G-01 — Fingerprint population in poll path

Resolved: fingerprint is populated for sensor poll events before dedup/publish.

## G-02 — Deduplicator wiring in poll path

Resolved: sensor poll path dedup is wired after history write and before EventBus publish.

## G-03 — Event source propagation

Resolved: poll path explicitly propagates `event.source` from reading metadata.

## G-06 — Tier 3/4 README accuracy

Resolved: runtime documentation now defines three runtime reasoning tiers: rule, local SLM, and gateway. Cloud reasoning is documented as a gateway backend, not a runtime tier.

## G-08 — `escalate_to` tier floor behavior

Resolved: elevator uses trigger `escalate_to` as a floor in tier selection.

## G-09 — Per-trigger approval timeout dispatch wiring

Resolved: `approval_timeout_seconds` flows into dispatcher approval wait logic.

## G-10 — Basic prompt interpolation + sanitization

Resolved: core placeholders are interpolated and sanitized; `{history.*}` interpolation is implemented with AST-safe parsing, load-time per-template cap (`max=16`), and deterministic unresolved substitution (`null`) with `WARNING` logs.

## G-07 — Skill signature verification enforcement

Resolved: community skill signatures are verified at load time against the hub root trust anchor.

## G-11 — Runtime health/status RPC socket

Resolved: runtime exposes read-only health snapshot RPC over Unix domain socket for CLI diagnostics.

## G-13 — Signing contract for community skills

Resolved: `signing/v1.md` defines Ed25519 signature format, public key format, bundled sentinel semantics, verification order, and deterministic test vectors for SDK/CLI/Hub/runtime consumers.

## G-04 — Gateway reasoning transport

Resolved: runtime now publishes Tier 3 reasoning requests over MQTT and receives
provider-neutral responses on `ori/{device_id}/reasoning/response`. Gateway/cloud
provider details remain outside the runtime boundary.

## G-05 — Gateway availability check

Resolved: runtime `CapabilityPostureTracker` tracks `ori/gateway/health` heartbeat
freshness and uses it before routing non-explicit deterministic escalation signals to
gateway reasoning. Gateway heartbeat publisher is fully implemented in `ori-gateway`
(`internal/heartbeat/publisher.go`).

## G-14 — Runtime export surface for gateway/reporting

Resolved: runtime exposes bounded MQTT exports for `health`, `sensor_history`,
`action_log`, `reasoning_log`, and `tier_c_decision_log`; gateway consumers do
not read runtime SQLite directly.

## G-12 — ContextEnricher device snapshot integration

Resolved (2026-06-08): `ori/reasoning/context_enricher.py` implements prompt-time
cross-sensor snapshot enrichment. Config lives under `reasoning.context_enricher`
(`enabled`, `staleness_window_ms`, `max_entries`, `include_sources`). Disabled by
default; fail-open. Staleness evaluated at prompt-build time, not event-emit time.
Tier D never reaches the enricher by construction. See `ori-runtime/DECISIONS.md`
2026-06-08 entry and `docs/CAPABILITY_MATRIX.md` for full implementation scope.

## G-17 — Gateway config surface not spec'd

Resolved (2026-06-09): `gateway-config/v1.md` documents all `gateway.yaml` keys,
types, defaults, and validation rules for all five top-level sections: `gateway`,
`provider`, `reporting`, `sim`, `fleet`. Separation between `provider` (Tier 3
reasoning) and `reporting.provider` (advisory/product) is made explicit. Tracked
by `ori-platform/ori-specs#5`.

## G-15 — Tier C enrichment MQTT transport wiring

Resolved: `ori-gateway` now defines Tier C enrichment topics, subscribes per
configured runtime device when `reporting.tier_c_enrichment.enabled=true`, and
publishes advisory-only responses on `ori/{device_id}/tier_c/enrichment/response`.
The gateway-side contract and handler are implemented in `internal/contracts`,
`internal/enrichment`, and `cmd/ori-gateway/app.go`. Runtime client integration
is a runtime-side follow-up, not a gateway contract gap.

## G-16 — `reasoning_log` export missing from gateway runtimeclient

Resolved: `ori-gateway` runtimeclient exposes `ReasoningLog` over the bounded
runtime export surface. Reporting/cloud consumers can request `reasoning_log`
without reading runtime SQLite directly.

## G-18 — Runtime/gateway MQTT hardening contract

Resolved: `gateway-api/v1.md`, `runtime-config/v1.md`, and `gateway-config/v1.md`
document runtime-gateway HMAC envelopes, verify-only previous secrets, AES-GCM
sensitive export encryption, runtime node heartbeat auth, gateway heartbeat auth,
and production broker posture attestation.

## G-19 — SMS webhook public-ingress hardening

Resolved: `runtime-config/v1.md` documents raw-body HMAC verification, nonce
replay protection, source CIDR requirements, no query-token fallback, and sender
allowlisting for runtime SMS webhook ingress. `gateway-config/v1.md` documents the
gateway SMS webhook signing bridge for providers that cannot sign webhooks.

## G-20 — Site health and weekly report delivery contracts

Resolved: `gateway-api/v1.md` and `gateway-config/v1.md` document the gateway
site-health HTTP projection, runtime posture enrichment, weekly report delivery
channels (`log`, `file`, `cloud`), customer-safe report payload constraints, and
ori-cloud identity ownership rules for report persistence.

## G-21 — Runtime public integration boundary

Resolved: `runtime-config/v1.md` documents the typed `ori.integration` rule
evaluation boundary and `py.typed` package marker used by product/demo consumers
such as `ori-energy`.
