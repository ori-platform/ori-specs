# Resolved Gaps

## G-01 — Fingerprint population in poll path

Resolved: fingerprint is populated for sensor poll events before dedup/publish.

## G-02 — Deduplicator wiring in poll path

Resolved: sensor poll path dedup is wired after history write and before EventBus publish.

## G-03 — Event source propagation

Resolved: poll path explicitly propagates `event.source` from reading metadata.

## G-06 — Tier 3/4 README accuracy

Resolved: runtime documentation now distinguishes implemented local tiers from reserved gateway/cloud tiers.

## G-08 — `escalate_to` tier floor behavior

Resolved: elevator uses trigger `escalate_to` as a floor in tier selection.

## G-09 — Per-trigger approval timeout dispatch wiring

Resolved: `approval_timeout_seconds` flows into dispatcher approval wait logic.

## G-10 — Basic prompt interpolation + sanitization

Resolved (partial scope): core placeholders are interpolated and sanitized; history-expression interpolation remains deferred.

## G-07 — Skill signature verification enforcement

Resolved: community skill signatures are verified at load time against the hub root trust anchor.

## G-11 — Runtime health/status RPC socket

Resolved: runtime exposes read-only health snapshot RPC over Unix domain socket for CLI diagnostics.
