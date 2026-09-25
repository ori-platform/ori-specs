# Open Gaps

New implementation gaps should be added here, naming the owning repository.

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
  `ori-skills-hub`.

- **`ori-sdk-python` authoring does not implement v2**
  ([skills-package/v2.md](../skills-package/v2.md)): the SDK accepts
  `action_tier: D` with no provenance concept, and has no manifest admission
  limits, workload budgets, reserved `config` names, identity bounds, or
  duplicate-action check. An author can build, validate and sign a package the
  runtime refuses. Tracked in `ori-sdk-python`.

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
  `ori-skills-hub`.

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

- **Installed target is not recorded**
  ([runtime-release-bundle/v1.md](../runtime-release-bundle/v1.md)): release
  signing, safe bundle verification, systemd lifecycle, health-gated rollback
  and end-to-end installer evidence are **implemented and evidenced**. The
  v2.4.0 release published four KMS-signed bundles, and installation was
  exercised on Ubuntu 24.04 `x86_64` with stock Python 3.12 and on Raspberry Pi
  OS Trixie `aarch64` with a trusted system-scope Python 3.12. Health-gated
  rollback has been proven end to end on `v2.5.0-rc.7` on Trixie `aarch64`.

  Stock Raspberry Pi OS Trixie ships Python 3.13 only. Both 3.13 targets are
  now published and the contract names them, and `v2.5.0-rc.7` was installed
  and proven on Trixie `aarch64` under system Python 3.13.

  A third supported interpreter widens a separate gap rather than closing one:
  re-running the installer on a host that has gained an interpreter silently
  resolves to a different target, because the installed state does not record
  which target it was built for. The reinstall works and says nothing. Tracked
  in `ori-runtime`.

## runtime-mobile/v2 implementation targets

- **No Android consumer verifies a payload**
  ([runtime-mobile/v2.md](../runtime-mobile/v2.md)): the conformance corpus,
  [payload-vectors-v2.json](../runtime-mobile/payload-vectors-v2.json), drives
  the runtime's producing signer and its reference verifier, and runtime
  v2.5.0-rc.10 published signed payloads. No Android application verifies a
  payload against the same bytes.

## evidence/v1 design targets

- **Anchor registry** ([evidence-exchange/v2.md](../evidence-exchange/v2.md)):
  the authority retains registrations and resolves them against held
  authorisations on main; the runtime produces no registration on main (it
  reports `pending_authorisation`), and no registration has crossed end to
  end.
- **Commissioning reference ingress and authorisation path**
  ([evidence-exchange/v2.md](../evidence-exchange/v2.md)): the contract now
  fixes that the authorisation reaches the authority through the
  organisational commissioning path and the device holds only its digest,
  delivered by the local bridge command in
  [operator-socket/v1.md](../operator-socket/v1.md). The evidence authority resolves
  a registration against held authorisations and refuses a courier-submitted
  authorisation at the transport. The device side is open:
  `ori-runtime` still models a `CommissioningAuthorisationSource` that would
  hand the registrar the full object, and reports `pending_authorisation`
  unconditionally; `ori-cli` has no `evidence commission` command
 ; the gateway courier refuses `commissioning_authorization` at
  ingress. The authority-side ingest of a signed authorisation is now
  [evidence-commissioning-ingest/v1.md](../evidence-commissioning-ingest/v1.md);
  the authority holds a library function that verifies a signed
  authorisation, with no ingest endpoint and no revocation path.
- **Evidence export ingestion** ([evidence-exchange/v2.md](../evidence-exchange/v2.md)):
  chain rows are marked `exported` locally but no authenticated receiver
  exists. The exchange contract specifies all eight artifacts. The runtime now
  verifies and applies three of the four inbound ones, all but the evidence
  disposition, and routes them from the transport
  specified in [gateway-api/v1.md](../gateway-api/v1.md), which also specifies
  byte-literal runtime-to-gateway carriage. The versioned evidence-carriage
  surface, including the fourth inbound type, is
  [gateway-evidence-carriage/v1.md](../gateway-evidence-carriage/v1.md), which
  no side implements yet. The separate
  [evidence-transport/v2.md](../evidence-transport/v2.md) fixes authenticated
  gateway-to-authority ingest. The gateway courier and the
  authority ingest are implemented on main, and the runtime
  now carries envelopes and checkpoints to the courier.
  No runtime release ships an authority-key registry, so receipts and epoch
  confirmations are still refused as unknown-key, and no exchange has been
  proven end to end through an authority.
  The current carriage also gives the authority no custody observation;
  `custodied, unreceipted` is therefore runtime-observed, while authority-side
  checkpoint scheduling/reporting remains implementation work. The authority
  implementation must not be treated as complete merely because the schema
  names a state that no path can establish.
  The transport's refusal policy (a closed reason list per status, delivery
  order and holds per device and lane, the unrecognised-outcome rule, and the
  admission size bound) has a corpus and a checker that derives the policy from
  the contract text, but neither implementation conforms yet: the authority
  reports its own storage failures as `409 conflict`, answers an unknown
  artifact version as `400 malformed`, can omit the digest from an authenticated
  `400`, and does not yet distinguish `409 pending_registration_conflict`, and
  the gateway courier's delivery order is gateway-wide rather than per device
  and lane.
  Retained refusals, the incident archive, retention bounds, evidence
  dispositions, pending-registration cancellation and identity replacement are
  specified with corpora, and with checkers that hold the contract's wording and
  replay models of the rules it states — models, not implementations, and a
  contradiction phrased outside a checker's patterns is not seen — and no
  repository implements them yet: the authority retains no refused
  bytes and issues no disposition or retention attestation, the courier still
  holds terminal refusals in the evidence lane and projects no incidents, the
  runtime acts on no disposition, and the commissioning path accepts no
  cancellation. The runtime's authority-key registry must carry the
  `evidence_authority_disposition` purpose before any disposition can verify.
  The same holds for checkpoint handoff order, the `queue_full` handoff, the
  registration-repair reservation, the stop projection and stopped custody, the
  `delivery_stop_status` field, and the epoch migration a release that adds a
  verification purpose declares: each has a corpus and a model checker of the
  same kind, and no repository implements it yet.
- **Tier C approval admission and dispatch**
  ([tier-c-approval/v1.md](../tier-c-approval/v1.md)): durable admission before
  approval, the commissioned binding and authority snapshot compared at
  admission, expiry against the creating process's monotonic deadline,
  immediate dispatch with nothing in front of it, the restart states
  (`proposal_aborted_restart`, `approval_aborted_undispatched` only on
  affirmative proof, `dispatch_outcome_unknown`, `dispatch_not_proven`), the
  terminal uncertainty record reserved at admission and the pending-outcome
  ceiling, the same-outcome block evaluated at proposal creation and again at
  reply admission (`proposal_blocked_uncertain_outcome`), and reconciliation
  are specified with a corpus and a model checker; the runtime implementation
  is in progress and not on main, and `action_records` in `runtime-health/v3`
  is not yet reported. The authenticated local operator reconciliation is
  closed at contract level: `evidence reconcile-tier-c` in
  [operator-socket/v1.md](../operator-socket/v1.md) is submitted to the running
  runtime over its local operator socket and authorized from kernel-provided
  peer credentials, with a closed reason and refusal set,
  root-or-installed-operator admission and an `identical_repeat` audit record,
  with a corpus and a model checker, and neither the runtime nor the CLI
  implements it. The runtime binds no operator socket for it yet, and the
  access-control entry that lets the installed operator connect without a group
  is contract text only. The kernel's peer credentials carry the effective user
  ID; the audit login user ID is read for the peer process pinned by
  `SO_PEERPIDFD`, which needs Linux 6.5 or later, and no implementation has
  shown it yet. The installer does not
  yet write the `operator-uid` file that names the installed operator identity,
  and [runtime-release-bundle/v1.md](../runtime-release-bundle/v1.md) does not
  yet require it, so until it does only root is admitted. Because the runtime
  is the only writer of its state store, admission does not depend on who can
  write the store, and the installed operator reconciles without store
  permissions. The commissioned-feedback
  mapping that proves an outcome is still undefined, so that form of
  reconciliation has no contract shape. No consumer enforces the release
  maximum on `approval_timeout_seconds` from
  [runtime-config/v2.md](../runtime-config/v2.md).
- **Evidence-carriage consumers cite gateway-api/v1**
  ([gateway-evidence-carriage/v1.md](../gateway-evidence-carriage/v1.md)): the
  runtime-gateway evidence MQTT surface is versioned in its own contract, and
  `gateway-api/v1` keeps the evidence carriage it shipped. `ori-runtime`'s
  `docs/CAPABILITY_MATRIX.md` and the `ori-gateway` documentation cite
  `gateway-api/v1` for the outbound and inbound evidence routes. Each must
  repoint to `gateway-evidence-carriage/v1` when it implements that contract,
  and until then a claim to `gateway-api/v1` is not a claim to the
  unconfigured-device acknowledgement rule, the `queue_full` handoff, the
  `superseded` refusal, the evidence disposition route or the per-device
  `evidence_delivery` projection.
- **Firmware-backed safety actuators in an in-force safety zone**
  ([device-provisioning/v1.md](../device-provisioning/v1.md),
  [evidence-exchange/v2.md](../evidence-exchange/v2.md)): a capability that
  depends on an unconfirmed provisioning grant is refused before a Tier C
  proposal is created and MUST NOT be admitted into an in-force commissioned
  safety binding, zone, or safety profile, and once one is in force evidence
  confirmation is never consulted for its Tier C or Tier D actions. No
  specification yet defines the commissioning and re-provisioning ceremony for a
  firmware-backed safety actuator, so such an actuator is unsupported in an
  in-force safety zone until one does. No firmware-backed actuator is wired to a
  zone today.
- **Tier C safe-default classification guard** (`ori-runtime`): the safe-default
  rule in [tier-c-approval/v1.md](../tier-c-approval/v1.md) is semantic, and
  this repository holds only a pinned instance of it. The runtime guard it
  requires — every runtime action exported or enumerated, each classified
  exhaustively, a new unclassified action failing, every action not classified
  informational and every physically capable action, whatever its tier or
  registry entry, rejected as a safe default, and a mutation that falsely
  changes a physical action's `physical` flag detected — is not implemented.
  Until it is, the runtime's `physical` and `safe_default_eligible` registry
  flags are the only enforcement, and the rule's own definition is not checked
  against them. Implementers note: `ori/reasoning/action_registry.py` in
  `ori-runtime` treats an action with no registry entry as safe-default
  eligible, which the contract now refuses outright.
- **Tier C decision export** ([gateway-api/v1.md](../gateway-api/v1.md)): the
  `tier_c_decision_log` export keeps its shipped shape and carries no
  `decision_state` or `expires_at_ms`. Exporting either needs gateway-api/v2 or
  a separately versioned export contract, and neither exists.
- **Offline token versions** ([offline-tokens/v2.md](../offline-tokens/v2.md)):
  the runtime's shipped offline token verifier implements
  [offline-tokens/v1.md](../offline-tokens/v1.md), accepts `action_scope: "*"`
  for Tier C and binds no proposal, and the CLI issues no v2 token, so neither
  conforms to the Tier C binding. A v2 token is signed in the
  `ori.offline_token.v2` domain, so a shipped v1 verifier rejects its signature;
  that holds only for tokens an issuer signs in that domain, and a signed
  corpus in this repository is its only executable check. The domain-separated
  signing overlaps in class with the firmware command and liveness
  signing-purpose separation deferred to v2.6.0, but is decided
  separately and is in v2.5.0.
- **events/v2 consumer proof** ([events/v2.md](../events/v2.md)): the Tier C
  approval record is a proposal awaiting ratification, and no record producer
  or consumer implements it. `scripts/check-tier-c-approval-vectors` holds
  events/v1 to its published sentences as a text guard. This guard proves only
  that the published v1 prose was not edited and that proposed v2 vocabulary
  did not enter v1. It does not prove wire compatibility or consumer behavior. Before
  events/v2 ratification or implementation, a data vector and independent v1/v2
  consumer checks are mandatory. A host-state Tier C action stays on events/v1 semantics
  and emits no events/v2 approval record until a resource-scoped approval
  contract exists.
- **Stopped-local evidence counts** ([runtime-health/v3.md](../runtime-health/v3.md)):
  `stopped_local_artifact_count`, `stopped_local_bytes` and
  `oldest_stopped_local_since_ms` are specified with a corpus and a model
  checker in the evidence-disposition corpus, including the handoff-time proof
  an acknowledgement needs to lower them and the exclusion of a retired
  identity's artifacts; no runtime reports them yet.
- **Evidence auditor access** ([evidence-audit/v1.md](../evidence-audit/v1.md)):
  Gateway ingest credentials are intentionally write-only and cannot enumerate
  or read devices. The separate contract now fixes the insurer/auditor caller,
  credential, authorisation, pagination and neutral refusal boundary. What
  remains open is its implementation and deployment proof; it must not be
  grafted onto the courier credential merely to claim that work is complete.
- **Epoch confirmation has no path back yet** ([evidence-exchange/v2.md](../evidence-exchange/v2.md)):
  the runtime confirms an epoch only from a signed epoch confirmation arriving
  through ingest, and artifacts now cross to the courier; but with no
  authority-key registry shipped and no authority reachable from a deployed
  gateway, no confirmation can arrive, so the confirmation gate stays closed in
  every deployment today.

- **Mixed-deployment return path** (`ori-gateway`;
  [gateway-evidence-carriage/v1.md](../gateway-evidence-carriage/v1.md),
  [gateway-config/v2.md](../gateway-config/v2.md)): authority artifacts are
  ordered per runtime device, each device declares its inbound carriage
  contract in `evidence.device_carriage` (absent is `gateway-api/v1`), an
  artifact the declared contract cannot carry is kept outside every lane and
  raises `return_path_incompatible`, and a mixed-site corpus with
  `scripts/check-gateway-return-path-vectors` holds the rules. Kept artifacts
  are idempotent by digest, keep arrival order, count toward the return
  store's item and byte capacity, are never evicted, and survive the device's
  removal from configuration; a response the return store cannot keep leaves
  its lane entry unadvanced and retried and blocks no other device.
  [gateway-config/v1.md](../gateway-config/v1.md) configures no key for the
  return store's capacity, so the bound a conforming gateway enforces is its
  own until a revision configures one. The gateway's
  return publisher keeps one head for the whole site
  (`internal/evidence/return_publisher.go`), so one device's unacknowledged
  receipt stops publication to every other device, and it neither reads a
  per-device declaration nor raises the fault.
- **Carriage capability at the authority** (the evidence authority;
  [evidence-transport/v1.md](../evidence-transport/v1.md)): the authority emits
  `refused_retained`, `retention`, `stop`, evidence dispositions, `507`,
  `retention_capacity_unavailable` and return artifacts on a refusal or a
  pending acceptance only to an ingest credential its credential registry
  records as conforming to `gateway-evidence-carriage/v1`, and answers every
  other credential with the legacy response, with a mixed-capability corpus
  and `scripts/check-evidence-transport-capability-vectors`. The credential
  registry records no capability yet. Registering each gateway's credential as
  conforming is part of deploying a conforming gateway (`ori-gateway`). A
  legacy gateway receives a perpetual retriable `503 unavailable` for an
  identity-stopped device, and on the shipped single-head courier that stalls
  every device at that gateway. `409 pending_registration_conflict` and
  `429 pending_registration_limit` map to legacy `503 unavailable`. The new
  refusal and stop policy is activated for a credential only after its gateway
  is upgraded and registered as conforming, so a legacy credential keeps legacy
  behaviour; the registry records no activation yet. During a mixed rollout
  the authority does not activate the new refusal policy for an epoch that does
  not declare `checkpoint_fifo_handoff_v1`; no authority implements that
  qualification.
- **Checkpoint rollback scope** (`ori-runtime` and the evidence authority;
  [runtime-evidence-anchor/v1.md](../runtime-evidence-anchor/v1.md),
  [evidence-exchange/v1.md](../evidence-exchange/v1.md)): a rollback is
  identity-scoped only under an epoch whose signed capability profile declares
  `checkpoint_fifo_handoff_v1` and only when it arrived through a conforming
  credential; otherwise it is `artifact_terminal`. The shipped runtime hands off
  a later checkpoint while an earlier one is in back-off
  (`ori/gateway/evidence_outbound.py`), so no release may declare the
  capability until its producer keeps FIFO order, and declaring it is an
  evidence-epoch migration. The authority must read the capability from the
  registration that established the epoch, and compare a checkpoint against
  the highest counters ever accepted for the identity: comparing against the
  most recently received checkpoint misses a rollback after a clock step. A
  shipped non-FIFO runtime behind a conforming gateway spends its retention
  allowance on benign reorders, and once that allowance is spent its evidence
  lane sits at `503 retention_capacity_unavailable` until the authority
  operator acts. This affects evidence only. The rollout qualification in
  [evidence-transport/v1.md](../evidence-transport/v1.md) keeps the new refusal
  policy inactive for such an epoch during a mixed rollout, and envelope forks
  may stay identity-scoped under it. The authority on main compares against
  the most recently received checkpoint.
- **Stopped custody at the gateway** (`ori-gateway`;
  [gateway-evidence-carriage/v1.md](../gateway-evidence-carriage/v1.md),
  [gateway-config/v1.md](../gateway-config/v1.md)): stopped entries leave
  `pending`, `blocked` and `devices` for `stopped_custody`, raise
  `reprovisioning_required`, and are bounded per identity and in total by the
  stopped-custody bounds, beyond which an artifact receives no custody and no
  acknowledgement, with no eviction; a courier opening above a bound opens,
  raises `stopped_custody_full` and delivers every lane that is not stopped.
  Each `stopped_custody` entry carries a required `scope`, `epoch` or
  `identity`, from the governing verified stop projection. A stopped-scope
  admission publishes `queued` only after the stopped bytes and their return
  obligation are durable, and for a delivery envelope only after its custody
  acknowledgement is durably staged; beyond a bound it publishes neither, and
  an entry already in custody moves to stopped custody even beyond a bound.
  The gateway counts stopped entries in `pending` and `blocked`, has no
  stopped-custody bound, carries no `scope`, and its branch refuses to open
  when its total retained bytes exceed the configured maximum.
- **Gateway transport text fixes** (`ori-gateway`;
  [gateway-evidence-carriage/v1.md](../gateway-evidence-carriage/v1.md),
  [evidence-transport/v1.md](../evidence-transport/v1.md),
  [evidence-exchange/v1.md](../evidence-exchange/v1.md)): the `queue_full`
  signal follows the published acknowledgement, since no durable record of it
  exists; an authenticated admission is carriage on a configured device's
  topic under the asserted broker posture, because MQTT 3.1.1 does not expose
  the publisher; a shipped gateway answers `malformed` for any `v` other than
  1, which is not terminal for the runtime; the routing projection is read by
  exact member name, where Go's `encoding/json` matches field names without
  regard to case and keeps the last of a duplicated name, so the courier needs
  an exact-key parse that refuses both; and a per-gateway ingest secret is a
  deployment obligation no party can detect.
- **TLS-only release of checkpoint and registration copies**
  ([evidence-transport/v1.md](../evidence-transport/v1.md)): a tracked
  limitation. A `200 accepted` for a checkpoint or an anchor registration
  carries nothing the gateway can verify, so the gateway releases its copy on
  the TLS-authenticated response alone. That is authority trust, not signed
  retention proof, and no document may describe it as cryptographic custody
  evidence. Closing it needs a signed acceptance the gateway can verify.
- **Tier C scope, safe-default intent and proposal integrity** (`ori-runtime`;
  [tier-c-approval/v1.md](../tier-c-approval/v1.md),
  [offline-tokens/v2.md](../offline-tokens/v2.md)):
  - Tier C admission, the offline-tokens/v2 binding and reconciliation apply to
    Tier C actions capable of changing physical state; a generic
    `coap_command` is refused as a Tier C proposal, and `terminate_process`
    stays at Tier C on the existing approval workflow, its tier unchanged. The
    general resource-scoped approval contract that would unify the host-state
    and Tier B workflows is a v2.6.0 item, tracked in `ori-specs`.
  - A safe default needs a registry capability classified `informational` and
    `safe_default_eligible`: `ActionCapability` gains a consequence class, a
    physical capability is never eligible, an action with no registry entry is
    ineligible, and the exhaustive classification guard pinned independently
    of the registry replaces the eligibility the registry now grants an
    unregistered action.
  - At most one durable safe-default intent per `proposal_id`, alert attempts
    through the outbox carrying the identifier, and idempotent logging by it.
  - A proposal row that cannot be committed is never sent; the safe default is
    attempted with a CRITICAL notification and never claimed durable.
  - The authority snapshot becomes the closed canonical object the contract
    defines; the runtime's snapshot today is
    `{"kind":"tier_c_approval","proposal_id":…}`, which binds nothing that can
    change and always matches.
  - An outcome the live runtime cannot append stays `pending`, or
    `unknown_live` at the ceiling, and is made `dispatch_outcome_unknown` or
    `dispatch_not_proven` at restart by the durable marker; the reply-time
    same-outcome block is evaluated inside the approval commit and counts
    `approved_pending_dispatch` and `dispatch_started`.
  - An offline v2 token is consumed by `token_id`, and a payload naming a
    member twice is refused.
  - No v1 token approves any Tier C action, host-state Tier C actions on the
    existing approval workflow included, and a host-state Tier C action has no
    offline-token approval path until a resource-scoped approval contract
    defines one. The shipped approval workflow accepts a v1 token, wildcard
    included, for `terminate_process`.
  - A physical Tier B action with `requires_approval` still accepts v1 and
    wildcard tokens on the existing approval workflow. No contract here decides
    that case; it is tracked in `ori-runtime` for v2.5.0.
- **Tier C proposals at graceful shutdown** (`ori-runtime`;
  [tier-c-approval/v1.md](../tier-c-approval/v1.md)): decided for the runtime
  and tracked in `ori-runtime`, not yet contract text. Graceful shutdown records
  `proposal_aborted_shutdown` and durably enqueues the non-actuating
  notification without awaiting provider delivery; restart converts any
  surviving open proposal to `proposal_aborted_restart`; both use the one
  safe-default intent keyed by `proposal_id`. An ordinary CLI client
  disconnect or interrupt cancels no runtime proposal unless an explicit
  authenticated cancellation operation exists. `proposal_aborted_shutdown` is
  not in the `decision_state` vocabulary of this revision; adding it needs the
  vocabulary, the severity table, the events/v2 proposal, the vectors and the
  model to change together.
- **Operator socket peer verification** (`ori-runtime`, `ori-cli`;
  [cli-commands/v1.md](../cli-commands/v1.md)): before trusting a response
  from the operator socket the bridge verifies the connected peer from
  kernel-provided credentials as the runtime service identity for the install
  scope, an explicit `--socket` included, and reports an unverified peer
  `runtime_unavailable`; a request naming `operation` twice is refused
  `invalid_arguments`. No bridge implements either.
- **events/v1 `approved` semantics** (`ori-runtime`;
  [events/v1.md](../events/v1.md)): events/v1 is unchanged, and `approved=true`
  means the operator supplied an affirmative token; it does not mean admitted
  or dispatched. Shipped main writes `approved=false` for a YES refused late at
  the resource gate (`ori/reasoning/action_dispatcher.py`), and the Tier D
  branch extends that to any admitted YES that did not dispatch. The runtime
  must restore the v1 meaning and carry a late gate refusal in the existing
  result fields; no erratum redefines the frozen field.
- **Local operator socket** (`ori-runtime`, `ori-cli`;
  [cli-commands/v1.md](../cli-commands/v1.md)): `evidence commission` and
  `evidence reconcile-tier-c` are submitted to the running runtime over its
  authenticated local operator socket, whose path is derived from the install
  scope (`/run/ori/` for a system install, `/run/user/<uid>/ori/` for a user
  scope) and which needs a search-permission access-control entry on the
  runtime directory as well as the connect entry on the socket; the unit
  creates that directory with mode `0700` under `UMask=0077`. The in-progress
  bridge `evidence commission`, on no main branch, reads and writes the state
  stores directly, needing
  store write permission and able to leave WAL files owned by its caller; it
  must move to the socket and write no store. The audit login user ID is read
  for a peer pinned by `SO_PEERPIDFD` and recorded `null` otherwise, and the
  recorded entry point is `local_operator_socket`. An installed runtime serves
  its health socket at `<install root>/data/health.sock`.
- **Evidence producer offer anchors** (`ori-runtime`;
  [evidence-exchange/v1.md](../evidence-exchange/v1.md)): a registration's
  first re-offer is measured from its first attempted offer and each later one
  from the previous attempted offer, persisted; the in-progress producer measures from `sealed_at_ms`. A damaged checkpoint copy and a copy naming another identity
  leave the active offer loop only into durable damaged or foreign-identity
  custody, never by deletion.

## firmware-telemetry/v1 proof targets

- **Layer 1 real-device proof** ([firmware-telemetry/v1.md](../firmware-telemetry/v1.md)):
  the producer (`ori-edge-firmware`) and the consumer (`ori-runtime`) implement
  and cite this contract — the signed telemetry envelope, capability manifest,
  heartbeat, `(boot_id, seq)` replay defence, signed fault events, and the
  local-interlock boundary — against shared cross-language golden vectors, and
  the anchor lifecycle and cross-store confirmation surface it resolves against
  are implemented in the runtime and the private evidence-store implementation.
  What remains is proof rather than definition: the live factory-fresh ESP32-S3
  provisioning and broker path, tracked in `ori-edge-firmware`.

## firmware-commands/v1 implementation targets

- **Runtime liveness adoption**
  ([firmware-commands/v1.md](../firmware-commands/v1.md)): the contract
  defines the signal, and the producer side is **implemented**: the signing
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
  `ori-edge-firmware` and `ori-specs`.

## firmware-commands/v1 protocol targets

- **Signing-purpose separation** ([firmware-commands/v1.md](../firmware-commands/v1.md)):
  command and liveness signatures are made with the same runtime command key
  over raw object bytes, so any runtime path able to sign liveness bytes can
  sign command bytes without passing the command path's checks. No such path
  exists today. A later protocol version separates the purposes, by a signing
  domain per message type or a separate liveness key, in this contract and in
  firmware together. Tracked in `ori-specs` for v2.6.0.

## firmware-mqtt-provisioning/v1 implementation targets

- **Signed transport-identity provisioning**
  ([firmware-mqtt-provisioning/v1.md](../firmware-mqtt-provisioning/v1.md)):
  the sealed firmware key/certificate lifecycle, provisioning-authority signer,
  shared vectors, bounded device verifier, and concrete delivery adapter are
  implemented and host-tested. What remains is the hardware-gated
  factory-fresh provisioning and broker-path proof tracked in
  `ori-edge-firmware`.

## signing/v1 implementation targets

- **Community skill artifact verification**
  ([signing/v1.md](../signing/v1.md)): the runtime implements the embedded
  strict canonical-manifest verifier and pins the shared vectors. The SDK
  implements the separate canonical-manifest and detached exact-artifact
  profiles against those vectors.

  The Hub publish pipeline verifies the author's detached artifact signature
  over the exact upload bytes before extraction (`hub/core/publish.py`).
  Remaining: pre-extraction install verification in `ori-cli`.
  Implementations must keep artifact and manifest verification as separate
  entry points.

## runtime-config/v2 implementation targets

- **No consumer, no producer** ([runtime-config/v2.md](../runtime-config/v2.md)):
  the closed provisioning document is a pre-ratification design target with a
  machine schema and a corpus. `ori-runtime` is the intended consumer and
  `ori-cloud` the intended producer; neither has started. Every shipped
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
  in `ori-runtime`.

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

- **The consumer is dormant** ([safety-profile/v1.md](../safety-profile/v1.md)):
  runtime v2.5.0-rc.8 ships the release-owned safety registry, typed
  evaluators, activation from accepted zones and durable trip state, and every
  profile it ships is a `candidate`, so the registry activates nothing. Every
  Tier D trip point on a device today is still an untyped number in a
  first-party `skill.yaml`.

- **Every shipped profile is a `candidate`, and a candidate activates nothing**
  ([safety-profile/v1.md](../safety-profile/v1.md)): the three profiles carry
  the numbers the legacy triggers use — `2.0×` capacity, `260.0 V`,
  `400.0 ppm` — and equivalence with a trigger that never reliably actuated is
  not a safety argument. Ratifying each needs what the circuit's limiting
  rating is, a validated RMS measurement window (target-measured only to
  2.4 A, and unmeasured near any trip point), and a
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
  binding field or evaluator rule exists to say so.

## commissioned-safety-binding/v1 implementation targets

- **No released producer**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the runtime consumer shipped in v2.5.0-rc.8 and verifies, retains and
  reports a binding. `ori-cli` produces, verifies, captures and signs bindings
  on its default branch and has no release. No device can activate an
  actuating safety profile, because every shipped profile is a `candidate`.

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
  use. Tracked in `ori-runtime`.

- **The shipped runtime example describes a cutoff that cannot fire**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  `ori-runtime/ori.yaml.example` declares `rated_capacity_amps: 10.0`, the
  electrical overcurrent profile in `safety-profile/v1` multiplies capacity by 5.0, and
  the clamp the runtime documentation recommends reads to 30 A. The trip point
  is 50 A on a sensor that saturates at 30 A, so the hazard arrives as a merely
  high reading and the Tier D condition never fires. Surfaced while authoring
  the binding vectors, and the reason this contract bounds the trip point rather
  than only the capacity.

- **The closed grammar has no non-Python enforcement**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  `ori-cli` captures, proves, signs and delivers a binding, and `ori-runtime`
  verifies, retains and drives the relay through it. No profile can activate
  until one is ratified.

- **Runtime actuation resolves through the commissioned outcome**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  runtime actuation resolves the legacy relay actions to a commissioned
  outcome through the zone in force; the bench verification waits on the
  `ori-runtime` cutover that makes the safety registry the sole Tier D path.

- **No device-side verification of a commissioned mapping**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  the contract requires a firmware device to hold the mapping in a
  commissioning-signed object it verifies itself, because a manifest
  `binding_ref` proves only that the firmware claims a relationship to a
  binding. Nothing on the device verifies anything of the kind today. Tracked
  in `ori-edge-firmware`.

- **Firmware board profiles are literals, not commissioned artifacts**
  ([commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md)):
  `ori-edge-firmware` honours D-021 structurally — the relay driver has no
  default mapping — but the ESP32 build supplies that mapping as a compile-time
  literal in `device/main/app_main.c`, and both `boards/` profile directories
  are empty. The bench value is annotated as unconfirmed by a terminal-state
  table, which is honest and is not a substitute for one.
