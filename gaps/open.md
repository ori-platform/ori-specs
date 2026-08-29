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
- **Commissioning reference ingress and authorisation path**
  ([evidence-exchange/v1.md](../evidence-exchange/v1.md)): the contract now
  fixes that the authorisation reaches the authority through the
  organisational commissioning path and the device holds only its digest,
  delivered by the local bridge command in
  [cli-commands/v1.md](../cli-commands/v1.md). `ori-verity` resolves a
  registration against held authorisations and refuses a courier-submitted
  authorisation at the transport (`ori-verity#45`). The device side is open:
  `ori-runtime` still models a `CommissioningAuthorisationSource` that would
  hand the registrar the full object, and reports `pending_authorisation`
  unconditionally; `ori-cli` has no `evidence commission` command
  (`ori-cli#34`); the gateway courier refuses `commissioning_authorization` at
  ingress. The authority-side ingest of a signed authorisation is
  `ori-specs#130`.
- **Evidence export ingestion** ([evidence-exchange/v1.md](../evidence-exchange/v1.md)):
  chain rows are marked `exported` locally but no authenticated receiver
  exists. The exchange contract specifies all seven artifacts. The runtime now
  verifies and applies the three inbound ones and routes them from the transport
  specified in [gateway-api/v1.md](../gateway-api/v1.md); that contract now also
  specifies byte-literal runtime-to-gateway carriage. The separate
  [evidence-transport/v1.md](../evidence-transport/v1.md) fixes authenticated
  gateway-to-authority ingest. The gateway courier (`ori-gateway#84`) and the
  authority ingest (`ori-verity#43`) are implemented on main, and the runtime
  now carries envelopes and checkpoints to the courier (`ori-runtime#439`).
  No runtime release ships an authority-key registry, so receipts and epoch
  confirmations are still refused as unknown-key, and no exchange has been
  proven end to end through an authority.
  The current carriage also gives the authority no custody observation;
  `custodied, unreceipted` is therefore runtime-observed, while authority-side
  checkpoint scheduling/reporting remains implementation work. The authority
  implementation must not be treated as complete merely because the schema
  names a state that no path can establish.
- **Evidence auditor access** ([evidence-audit/v1.md](../evidence-audit/v1.md)):
  Gateway ingest credentials are intentionally write-only and cannot enumerate
  or read devices. The separate contract now fixes the insurer/auditor caller,
  credential, authorisation, pagination and neutral refusal boundary. What
  remains open is its implementation and deployment proof; it must not be
  grafted onto the courier credential merely to claim that work is complete.
- **Epoch confirmation has no path back yet** ([evidence-exchange/v1.md](../evidence-exchange/v1.md)):
  the runtime confirms an epoch only from a signed epoch confirmation arriving
  through ingest, and artifacts now cross to the courier; but with no
  authority-key registry shipped and no authority reachable from a deployed
  gateway, no confirmation can arrive, so the confirmation gate stays closed in
  every deployment today.

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

## runtime-config/v2 implementation targets

- **No consumer, no producer** ([runtime-config/v2.md](../runtime-config/v2.md)):
  the closed provisioning document is a pre-ratification design target with a
  machine schema and a corpus. `ori-runtime` #332 is the intended consumer and
  `ori-cloud` #19 the intended producer; neither has started. Every shipped
  runtime still loads the open v1 surface, in which an undeclared nested key
  is carried silently and `device.rated_capacity_amps` is a provisioning-signed
  value.

- **No consumer holds a configuration set**
  ([runtime-config-orchestration/v1.md](../runtime-config-orchestration/v1.md)):
  the orchestration contract fixes anchors, anchor-wide selection, the
  inventory invariant that licenses actuation, startup and runtime behaviour
  on partial failure, entitlement retention and the legacy latch, with a
  state-transition corpus. Nothing implements it: `ori-runtime` verifies one
  document against one anchor, reads the entitlement anchor from inside that
  document, and holds no notion of an inventory generation. Consumer work is
  `ori-runtime` #332.

- **`signing_key` and the previous provisioning anchor are contract text only**
  ([runtime-config-signing/v1.md](../runtime-config-signing/v1.md)): the
  design-target section adds them so that `wrong_authority` is reachable for
  the provisioning document and the provisioning key can rotate. The runtime's
  signature path neither reads `signing_key` nor configures a previous anchor.

- **`skills[].config` is closed to one key**
  ([runtime-config/v2.md](../runtime-config/v2.md)): the shipped examples
  carry skill settings that never reach the skill and are refused under v2
  until `skills-package/v3` gives them a signed schema to be validated
  against. The delegation flips to pass-through when a runtime implements v3.

## safety-profile/v1 implementation targets

- **No consumer** ([safety-profile/v1.md](../safety-profile/v1.md)): the
  contract is a pre-ratification design target with a profile set and a
  corpus. `ori-runtime` #324 is the intended consumer — safety registry,
  typed evaluators, activation from accepted zones, durable trip state — and
  nothing implements any of it. Every Tier D trip point on a device today is
  still an untyped number in a first-party `skill.yaml`.

- **Every shipped profile is a `candidate`, and a candidate activates nothing**
  ([safety-profile/v1.md](../safety-profile/v1.md)): the three profiles carry
  the numbers the legacy triggers use — `2.0×` capacity, `260.0 V`,
  `400.0 ppm` — and equivalence with a trigger that never reliably actuated is
  not a safety argument. Ratifying each needs what the circuit's limiting
  rating is, a validated RMS measurement window (`ori-runtime` #398), and a
  time-current answer; until then a consumer MUST NOT activate or execute
  them. Separately, `battery_emergency_cutoff` and `cpu_overheating` migrate
  out of Tier D because neither reaches an actuator. All of it is recorded in
  the contract's open questions.

- **No independent verifier** ([safety-profile/v1.md](../safety-profile/v1.md)):
  the checker in this repository recomputes the corpus from the contract's
  rules, which proves the corpus agrees with the text and nothing about a
  second implementation. Ratification waits for a verifier in another language
  sharing no code with the runtime's evaluator.

- **Measurement-loss isolation is unexpressed**
  ([safety-profile/v1.md](../safety-profile/v1.md)): v1 profiles hold state
  and alert on sustained measurement loss. Whether a zone should open its
  protected circuit instead is a commissioned property of the load, and no
  binding field or evaluator rule exists to say so. Related to `ori-runtime`
  #397 and #398.

## commissioned-safety-binding/v1 implementation targets

- **Producer core only, no consumer**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the contract is a pre-ratification design target. `ori-cli` builds and signs
  a binding and reproduces the corpus in Go; the capture, physical proof and
  delivery ceremony in `ori-cli` #34 remain open, and `ori-runtime` #324 is
  the intended consumer. Until both exist, no device can activate an actuating
  safety profile under this contract's rules.

- **The unsafe NC rule survives in governing documents**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the contract's "contact type is not an input" rule is what
  [safety-profile/v1.md](../safety-profile/v1.md) now defers to, but the
  operator-facing wiring instructions in `ori-runtime` (`CLAUDE.md`,
  `docs/RASPBERRY_PI_SUPPORT.md`) still instruct NC wiring as proof of fail-safe
  behaviour. Correcting the contract without correcting the documents an
  installer actually reads leaves the unsafe rule in force where it does damage.

- **Runtime actuation does not route through a commissioned mapping**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  in `ori-runtime`, semantic relay actions (`trip_relay`, `release_relay`,
  `close_gas_valve`) select a coil state directly rather than resolving an
  outcome through a binding. Accepting a mapping at connect time without
  routing actuation through it proves configuration transport, not physical
  use. Tracked in `ori-runtime` #397.

- **The shipped runtime example describes a cutoff that cannot fire**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  `ori-runtime/ori.yaml.example` declares `rated_capacity_amps: 10.0`, the
  electrical overcurrent profile in ori-specs#71 multiplies capacity by 5.0, and
  the clamp the runtime documentation recommends reads to 30 A. The trip point
  is 50 A on a sensor that saturates at 30 A, so the hazard arrives as a merely
  high reading and the Tier D condition never fires. Surfaced while authoring
  the binding vectors, and the reason this contract bounds the trip point rather
  than only the capacity.

- **The closed grammar has no non-Python enforcement**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the grammar is now normative and carries vectors, so a conforming verifier in
  any language is held to it. Nothing outside the reference Python verifier
  enforces it yet, which is the same gap as cross-language reproduction and
  closes with it.

- **Cross-language independence of the vector corpus is unproven**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the generator and the verifier are both Python and were written from the
  contract rather than from each other. That demonstrates internal consistency,
  not the cross-language byte agreement the canonical-JSON rules exist for. The
  Go producer in `ori-cli` #34 is the intended second implementation.

- **No device-side verification of a commissioned mapping**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the contract requires a firmware device to hold the mapping in a
  commissioning-signed object it verifies itself, because a manifest
  `binding_ref` proves only that the firmware claims a relationship to a
  binding. Nothing on the device verifies anything of the kind today. Tracked
  in `ori-edge-firmware` #79.

- **Firmware board profiles are literals, not commissioned artifacts**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  `ori-edge-firmware` honours D-021 structurally — the relay driver has no
  default mapping — but the ESP32 build supplies that mapping as a compile-time
  literal in `device/main/app_main.c`, and both `boards/` profile directories
  are empty. The bench value is annotated as unconfirmed by a terminal-state
  table, which is honest and is not a substitute for one.
