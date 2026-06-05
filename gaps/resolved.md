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

Resolved: runtime tracks `ori/gateway/health` heartbeat in capability posture and
uses freshness before routing non-explicit deterministic escalation signals to
gateway reasoning.

## G-14 — Runtime export surface for gateway/reporting

Resolved: runtime exposes bounded MQTT exports for `health`, `sensor_history`,
`action_log`, `reasoning_log`, and `tier_c_decision_log`; gateway consumers do
not read runtime SQLite directly.
