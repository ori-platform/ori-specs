# Open Gaps

New implementation gaps should be added here with repo tracking links.

## skills-package/v2 adoption targets

Downstream repos were surveyed against v2 rather than repointed. A v1 reference
is not evidence of v2 compliance, so references stay on v1 where v1 is the
accurate description of what a repo implements.

- **`ori-skills-hub` admission does not implement v2**
  ([skills-package/v2.md](../skills-package/v2.md)): the Hub permits community
  `action_tier: D`, cannot detect `hooks.py` because it never opens package
  files, and has no manifest admission limits, workload budgets, reserved
  `config` name rejection, or identity length/character bounds. It therefore
  admits packages that runtime >= 2.4.0 refuses — a package publishes cleanly
  and then fails to load on device. Its duplicate-action check, Tier D action
  reference rule and history-placeholder cap already match. Tracked in
  `ori-skills-hub` #49.

- **`ori-sdk-python` authoring does not implement v2**
  ([skills-package/v2.md](../skills-package/v2.md)): the SDK accepts
  `action_tier: D` with no provenance concept, and has no manifest admission
  limits, workload budgets, reserved `config` names, identity bounds, or
  duplicate-action check. An author can build, validate and sign a package the
  runtime refuses. Tracked in `ori-sdk-python` #48.

- **`ori-cli` — no gap found.** It references no package contract and passes
  runtime bridge JSON through without decoding fields, so the `skills-list`
  aggregate changes in runtime 2.4.0 (`valid` becoming conjunctive, plus
  `activatable` and `unactivatable_count`) do not affect it. Recorded so the
  survey is not repeated.

- **Published community packages using hooks or Tier D**
  ([skills-package/v2.md](../skills-package/v2.md)): unloadable on runtime
  >= 2.4.0. Community packages are YAML-only until
  [skill-hook-isolation/v1.md](../skill-hook-isolation/v1.md) is implemented.
  Whether any published package is affected is unestablished; raised in
  `ori-skills-hub` #49.

## skill-hook-isolation/v1 design targets

- **Isolated community hook execution**
  ([skill-hook-isolation/v1.md](../skill-hook-isolation/v1.md)): community hook
  execution is disabled in `ori-runtime` v2.4.0. The in-process loader was
  removed rather than hardened — its restricted namespace left the object graph
  reachable, and its import finder used `find_module`, removed in Python 3.12,
  so it failed open on Ubuntu 24.04. Nothing replaces it yet: the worker model,
  artifact digests binding `hooks.py`, environment and filesystem visibility,
  resource/process/IPC limits, capability quotas, signer revocation and
  anti-rollback are all defined as design targets and none is implemented.
  Community skills are YAML-only until this lands.

- **Artifact receipts binding executable bytes**
  ([skill-hook-isolation/v1.md](../skill-hook-isolation/v1.md)): the signature
  in [signing/v1.md](../signing/v1.md) covers the canonicalised `skill.yaml`
  manifest only. No receipt binds `hooks.py` or other executable content, so a
  valid signed manifest can be paired with different hook bytes. Producer
  (Hub, SDK build path, or installer) is undecided.

- **Offline revocation and anti-rollback**
  ([skill-hook-isolation/v1.md](../skill-hook-isolation/v1.md)): signed skills
  have no runtime revocation, expiry, or monotonic version policy, so an older
  legitimately signed vulnerable package can be replayed. Distribution to
  devices that are offline for extended periods by design is undecided.

- **Transactional reload rollback**
  ([skill-hook-isolation/v1.md](../skill-hook-isolation/v1.md)): `ori-runtime`
  v2.4.0 validates the full registration plan before removing existing handlers
  and accounts subscriptions by active handler count, so ordinary reloads are
  sustainable. Rollback of a failure *during* registration is not implemented —
  the runtime logs at CRITICAL and re-raises, leaving an incomplete graph.

## runtime-release-bundle/v1 implementation target

- **Rollback proof on a failing artifact**
  ([runtime-release-bundle/v1.md](../runtime-release-bundle/v1.md)): release
  signing, safe bundle verification, systemd lifecycle, health-gated rollback
  and end-to-end installer evidence are **implemented and evidenced**. The
  v2.4.0 release published four KMS-signed bundles, and installation was
  exercised on Ubuntu 24.04 `x86_64` with stock Python 3.12 and on Raspberry Pi
  OS Trixie `aarch64` with a trusted system-scope Python 3.12. `ori-runtime`
  #273 is closed.

  What remains is narrower than the entry it replaces: rollback is implemented
  but has never run end to end. Proving it needs a candidate that installs,
  activates, and *then* fails its own post-install diagnosis with exactly
  `post_install_health_failed` — a tampered bundle cannot stand in, because
  verification refuses it before anything is installed. The evidence harness
  carries the claim as a distinct phase and records it BLOCKED until such an
  artifact exists. Tracked in `ori-runtime` #335.

  Stock Raspberry Pi OS Trixie ships Python 3.13 only, which no published
  bundle targets. Tracked in `ori-runtime` #328.

## evidence/v1 design targets

- **Anchor registry** ([evidence-exchange/v1.md](../evidence-exchange/v1.md)):
  no off-device store binds `device_id` to `initial_pubkey_hex`; the anchor is
  only logged at first provisioning. The exchange contract now places
  registration over the gateway rather than a manual provisioning step, and
  requires the evidence authority to retain the verification binding
  independently. What remains open is the implementation, not the ownership.
- **Evidence export ingestion** ([evidence-exchange/v1.md](../evidence-exchange/v1.md)):
  chain rows are marked `exported` locally but no authenticated receiver
  exists. The exchange contract specifies the artifacts — envelope, custody
  acknowledgement, receipt, epoch confirmation — and none is implemented.
- **On-device evidence topology** ([evidence-exchange/v1.md](../evidence-exchange/v1.md)):
  the runtime confirms epochs against a chain loaded in its own process, which
  the exchange topology rules out. Nothing yet crosses the device boundary, so
  the confirmation gate cannot be satisfied in the deployment the contract
  describes.

## runtime-health/v2 adoption target

- **`evidence.artifact_version` still reported**
  ([runtime-health/v2.md](../runtime-health/v2.md)): v2 removes the field, and
  `ori-runtime` still emits it because it implements v1. The disclosure audit
  in `ori-runtime` #327 removed it briefly and restored it — a published
  contract is not a runtime's to change unilaterally, however thin the
  exposure.

  `ori-cli` both reads and **renders** it: `ori doctor` prints
  `artifact: <version>` in its Evidence section, so the operator-facing
  disclosure this closes currently lives in the CLI rather than the runtime.
  Removal will not break it — the field is parsed optionally and the render is
  guarded on non-empty — but the CLI should drop both deliberately, since a
  disclosure that disappears by accident can reappear the same way.
  `ori-gateway` does not consume it; its projection reads the heartbeat block,
  which never carried the field.

  Remaining: the runtime adopts v2 and drops the field, `ori-cli` drops the
  field and its render, and v2 then stops being a design target. Tracked in
  `ori-runtime` #327 and `ori-cli` #35.

## firmware-telemetry/v1 proof targets

- **Layer 1 real-device proof** ([firmware-telemetry/v1.md](../firmware-telemetry/v1.md)):
  the producer (`ori-edge-firmware`) and the consumer (`ori-runtime`) implement
  and cite this contract — the signed telemetry envelope, capability manifest,
  heartbeat, `(boot_id, seq)` replay defence, signed fault events, and the
  local-interlock boundary — against shared cross-language golden vectors, and
  the anchor lifecycle and cross-store confirmation surface it resolves against
  are implemented in the runtime and the private evidence-store implementation.
  What remains is proof rather than definition: the live factory-fresh ESP32-S3
  provisioning and broker path, tracked in `ori-edge-firmware` #12 and #14.

## firmware-telemetry/v1 implementation targets

- **`storage_degraded` producer adoption**
  ([firmware-telemetry/v1.md](../firmware-telemetry/v1.md)): the code and its
  late-reporting semantics are defined here, and receiver acceptance **landed
  in `ori-runtime` #270**. The gap is now entirely producer-side: **no producer
  emits the code.** `ori-edge-firmware` currently discards the
  result of appending a signed envelope to its offline buffer, so a
  measurement can consume a `(boot_id, seq)` pair and then vanish with no
  durable record.

  The remaining device-side work is a latch held outside the failed store,
  cleared only once the resulting fault is itself durably stored, with
  emission after recovery or on the next boot — the fault publisher writes to
  the same buffer whose failure it would report. A shared golden fault vector
  follows that implementation and removes the single-code exception asserted
  in `ori-runtime`'s fault-vector coverage test. Tracked in
  `ori-edge-firmware` #62.

  Note the receiver cannot substitute freshness analysis for this code: `seq`
  comes from a reservation ceiling, so a reboot legitimately skips a range and
  gaps are expected rather than diagnostic.

- **Runtime liveness adoption**
  ([firmware-commands/v1.md](../firmware-commands/v1.md)): the contract landed
  in `ori-specs` #45, and the producer side is **implemented**: the signing
  path, the publisher and its scheduler, durable per-device `runtime_seq` in
  the state store, and shared cross-language golden vectors. Firmware
  implements the verifier, its ingress path and its broker ACL, and consumes
  the signal as **shadow observability only**.

  So the definition and both implementations exist; what is missing is that
  nothing yet *acts* on it. `ori-edge-firmware` still derives
  `runtime_reachable` from broker connectivity, so a live broker with a dead
  runtime leaves the Local Interlock suppressed while nothing upstream can
  respond — the supervision gap this signal exists to close remains open in
  practice.

  Remaining: the `runtime_reachable` switchover, sequenced last because it
  changes when a physical backstop is allowed to act; hardware-in-the-loop
  evidence for the broker-alive/runtime-dead case; and the safe-maintenance
  lifecycle, since manifest transitions currently lapse supervision and no
  unattended physical load should depend on it until they do not. Tracked in
  `ori-edge-firmware` #68 and `ori-specs` #49.

## firmware-mqtt-provisioning/v1 implementation targets

- **Signed transport-identity provisioning**
  ([firmware-mqtt-provisioning/v1.md](../firmware-mqtt-provisioning/v1.md)):
  the sealed firmware key/certificate lifecycle, provisioning-authority signer,
  shared vectors, bounded device verifier, and concrete delivery adapter are
  implemented and host-tested. What remains is the hardware-gated
  factory-fresh provisioning and broker-path proof tracked in
  `ori-edge-firmware` #12, #14, and #48.

## signing/v1 implementation targets

- **Community skill artifact verification**
  ([signing/v1.md](../signing/v1.md)): the runtime implements the embedded
  strict canonical-manifest verifier and pins the shared vectors. The SDK
  implements the separate canonical-manifest and detached exact-artifact
  profiles against those vectors.

  The Hub now *has* an artifact verifier — `verify_artifact_signature` in
  `hub/security/signing.py` — but **nothing in the publish pipeline calls it**;
  every reference outside its definition is a test. `ori-skills-hub` #4 and #9
  are closed without wiring it in, so a verifier that exists and is never
  invoked is the current state rather than an absent one. That distinction
  matters: the code will pass review and the property will not hold.

  Remaining: invoking artifact verification in the Hub publish pipeline, author
  signing end to end, and pre-extraction install verification (`ori-cli` #10).
  Implementations must keep artifact and manifest verification as separate
  entry points.
