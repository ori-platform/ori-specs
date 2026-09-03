# Qualification Session Procedure

> Status: Design target, pre-ratification. Companion to
> [safety-qualification-fixture/v1.md](v1.md), which owns the artifact and the
> authority; this document owns how a session is conducted and what it
> establishes. No session has been run under it, and none can be until that
> contract's exposure ceilings are ratified. Amendable in place under the
> [pre-ratification exception](../VERSIONING.md#pre-ratification-exception).

A qualification session puts an unratified Tier D condition in charge of a real
circuit for a bounded window. This document is the procedure that makes that
acceptable, and the source from which the fixture contract's three exposure
ceilings are derived.

It is normative about what a conforming session does. It is not a site guide:
which clamp, which contactor, which load belongs to the installation's own
commissioning record.

## What A Session Can And Cannot Establish

**Two classes of session exist and they are not interchangeable.** Confusing
them is the failure this document most needs to prevent, because the safe one
looks like the valuable one.

| Class | How the condition is reached | Establishes | Does not establish |
| --- | --- | --- | --- |
| **Mechanism** | A bench circuit whose true limiting capacity is genuinely low, crossed by a safe load at low energy | That the sensing path, evaluator, journal, actuation seam and commissioned mapping carry a reading through to a commanded outcome, and how long that takes | Anything about whether the trip point protects against real fault energy, or discriminates against a real load's transients |
| **Hazard** | A real over-condition is induced on a real circuit | Protection and discrimination — dossier argument 3 | Nothing a mechanism session establishes is superseded; the two are complementary |

**A bench runs mechanism sessions**, and the trip point is brought within reach
of a safe load by choosing a circuit whose limiting capacity is genuinely low —
never by declaring a lower one.

### The Commissioned Capacity Must Be True Of The Circuit

**A rated capacity is a safety parameter, not a test dial.** Commissioning a
capacity chosen to make an ordinary load cross the trip point manufactures a
trip from a false claim: the binding then records a capacity that is not the
circuit's, the multiplier is applied to a number nothing limits, and every
observation made under it describes a circuit that does not exist. A session
run that way is void, and so is the binding that enabled it.

It is also self-defeating. Dossier argument 1 exists to establish that the
quantity the multiplier is applied to is the quantity that limits the circuit.
A campaign whose own bench falsifies that field cannot be evidence for it, and
normalises the practice on every site that copies the bench.

A conforming bench circuit is instead one that **really is limited at a low
current** by a series element the binding can name: its conductor, a
termination, a connector, or the switching device's contacts — the closed
vocabulary of
[`rated_capacity.limiting_element`](../commissioned-safety-binding/v1.md#what-rated_capacity_amps-is).
That rating is documented, its `provenance` recorded honestly, and the
limiting-rating semantics stated exactly as argument 1 will require of a site.
A safe load may then cross `2.0×` of a capacity that remains true.

**A fuse or breaker cannot establish `rated_capacity_amps`, so a
fuse-limited bench circuit does not qualify.** A protective device's rating is
not what the circuit can carry: it is the current at which something else opens
first, and the conductor behind it keeps its own ampacity. Taking it as the
capacity would put a profile's trip point above a device that already acts,
which is the same exclusion the binding contract states for an upstream
protective device. Build the bench limit into a series element instead.

**This makes a mechanism session a real over-capacity condition at low energy**,
which is stronger than the false-capacity alternative and still not a hazard
session: nothing establishes behaviour against real fault energy, or against
the transients a real load produces. It is also why a demonstration of a fan
losing power must never be described as a demonstration that the profile
protects anything.

Hazard sessions have their own procedure in
[hazard-session.md](hazard-session.md), which splits them further: the
**discrimination** half needs no laboratory and carries no hazard beyond
ordinary operation of the load, and is expected to carry most of argument 3;
the **protection** half needs a current-limited rig with independent upstream
protection. Whether the first ratification requires any protection run is a
[dossier](../safety-profile/ratification.md#sufficiency) question, not a
procedural one.

## Preconditions

A session MUST NOT open unless all of the following hold. They are checked and
recorded, not assumed.

### On the device

- The commissioned binding for the zone is **in force** — both proof legs,
  circuit and control — and its hash matches the fixture's.
- The actuator has been confirmed drivable since the last start.
- The deployment profile is `development` or `staging`; `production` refuses.
- A conforming monotonic time source is available.
- The durable session state is readable, and sessions remain against
  `max_sessions`.

### On the circuit

- The zone's commissioned `rated_capacity` is **true of the circuit**: its
  limiting element is identified, its rating documented, its `provenance`
  recorded, and the limiting-rating semantics stated. A capacity selected to
  place the trip point conveniently refuses the session.
- The trip point falls within the sensor's declared range, and the load's
  normal operating value falls below it by a margin the operator records. A
  session whose baseline sits at the trip point measures noise.
- The zone's commissioned `de_energised_terminal_state` is known to the
  operator, out loud, before consent. What the load does when the coil drops is
  the difference between a session that fails safe and one that fails closed
  onto a live load.
- The load can be interrupted without damage. Qualification interrupts it,
  possibly repeatedly.

### In the room

- **An independent means of removing power, not under the runtime's control**,
  within reach of the person supervising. This is the one precondition with no
  substitute: the mechanism being tested is the mechanism that would otherwise
  stop the hazard, and a session exists precisely because nobody yet trusts it.
  A session supervised only through the runtime's own actuator has no fallback
  when the thing under test is what failed.
- A person present who can operate that isolation, who is not simultaneously
  driving the console. Consent taken over a remote session satisfies the
  fixture contract's technical conditions and satisfies nothing here.

## The Session

Steps 1 to 4 precede the window; the fixture contract's durable session
consumption happens at step 5.

1. **Verify and state.** Confirm the fixture is accepted and read back, aloud
   or to the console: the fixture hash and expiry, sessions remaining, the
   binding hash, the zone, the profile, the computed trip point and its unit,
   the outcome that will be commanded, the coil state that outcome resolves to
   under this zone's mapping, what losing coil power does to this zone, and
   that a window closing while armed leaves the circuit closed and unprotected.
2. **Establish the baseline.** With the circuit in its ordinary state, record
   readings for a stated interval: value, unit, quality, and their spread. A
   session whose baseline was never recorded cannot distinguish a trip from a
   sensor artefact afterwards.
3. **Confirm the fallback.** Physically confirm the independent isolation
   works, before anything is armed. Not a glance at a switch — operate it, see
   the load stop, restore it.
4. **Consent.** Interactively, at the controlling terminal, per the fixture
   contract. Consent is refused if any precondition above is unmet.
5. **Open the window.** The session is durably consumed here, before the pair
   can evaluate, arm, or actuate.
6. **Arm.** The protected circuit closes. Record what the load and the sensor
   do on closing; a load that does not start is a commissioning fault
   discovered before a trip rather than during one.
7. **Approach.** Bring the observed quantity toward the trip point in a
   controlled, reversible way, recording continuously. Stop short at least once
   and hold: a **negative run**, in which the condition is approached and not
   crossed and nothing fires, is evidence about discrimination and is recorded
   as a session outcome in its own right.
8. **Cross.** Take the quantity past the trip point and observe. What is
   recorded is what happened: the reading that satisfied the condition, the
   verdict, `intent_append_ms`, `command_issue_ms`, the command status, and
   what the load actually did — observed at the load, not inferred from the
   command.
9. **Observe the latch.** Confirm the pair is `tripped`, that the circuit
   stayed open, and that no reading, including a return to normal, moved it.
10. **Restore, then close — in that order.** If the pair tripped and the
    circuit is to be restored through the runtime, perform the local reset
    **while the window is still open**: reset is refused with
    `no_evaluation_authority` once the pair may no longer evaluate, and no
    later runtime action will close that circuit. Then close the window.
    Record which route was used, and the final state of the pair and the
    circuit.

    Leaving the pair tripped and the circuit open is a legitimate ending, not a
    failure — it is what the trip commanded. Choosing it deliberately, rather
    than discovering it after the window has closed, is the difference this
    step exists to make.

**Abort at any step** by operating the independent isolation, then closing the
window. An aborted session is recorded with its reason, stays consumed, and is
evidence: the most valuable session in a campaign is often the one that ended
because something behaved unexpectedly.

### What Is Recorded

The [qualification record](v1.md#the-qualification-record) fields are the
minimum, and this procedure adds three things a consumer cannot capture on its
own, supplied by the operator and retained with the record: the observed
baseline and its spread, the observed behaviour at the load at each actuation,
and the reason for any abort. A record whose only witness is the runtime cannot
support argument 5, because the runtime does not know what the load did.

## Deriving The Three Exposure Ceilings

The fixture contract states no ceilings and refuses every fixture until they
exist. They are derived from this procedure, and each derives from a different
measured quantity.

| Ceiling | Derives from | Currently |
| --- | --- | --- |
| `session_limit_seconds` maximum | The duration of a full window: fixed overhead, the operator-paced approach and hold, the trip-and-observe tail, and one abort and restore | **Partly measurable by rehearsal; the tail is not.** See below |
| `max_sessions` maximum | Runs needed per fixture: the dossier's sufficiency for the arguments a bench can close, times an allowance for aborted and negative runs, for one device and binding | **Underived** — needs the dossier's per-profile sufficiency |
| Validity window maximum | The span of a qualification campaign against one device and binding, bounded also by the worst-case revocation delay, since a fixture cannot be withdrawn from a device nobody can reach | **Underived** |

Each ceiling is a ceiling on what an issuer may declare, not a target. Sizing
them to the longest imaginable campaign defeats them; sizing them to a single
comfortable run makes the instrument unusable and invites a request to raise
them, which is worse.

### Rehearsals, And What They Cannot Reach

Two rehearsals can be run before any of this exists in code. **Both are
non-actuating.** Neither commands a coil, neither arms a pair, and neither
invokes the commissioning proof operation.

**The proof operation is not available here and must not be borrowed.** It acts
on a **provisional** binding and is
[unavailable once the zone is in force](../commissioned-safety-binding/v1.md#the-commissioning-proof-operation),
while a fixture requires a binding **in force** and refuses a provisional one
with `binding_not_in_force`. The two lifecycles are disjoint by design: one
exists to establish a control path nobody has proven, the other to exercise a
condition over a control path already proven. Reaching for the first to time
the second would mean either weakening its provisional-only rule or bringing a
proven zone backwards, and both are worse than lacking a measurement.

| Rehearsal | Runs | Yields |
| --- | --- | --- |
| **Procedural** | The verification readback, baseline capture, fallback confirmation, consent dialogue, and the recording discipline of every step | The fixed operator overhead that brackets a session |
| **Load approach** | The same physical approach as step 7, on the same circuit and load, with nothing armed and no pair active — the load is driven through the same profile of values while readings and wall time are recorded | The operator-paced approach and negative-run hold durations, which are expected to dominate a session |

The load-approach rehearsal is safe because it is the ordinary operation of a
load with an observer watching numbers. It is legitimate on an in-force binding
because it actuates nothing: no coil is commanded, in either direction, and the
runtime's role is to report readings.

**What no rehearsal reaches:**

- `intent_append_ms` and `command_issue_ms`. Nothing trips, so nothing is
  timed. These wait for real sessions, which is the ordering the dossier's
  latency argument already implies.
- The trip-and-observe tail — steps 8 and 9 — and the abort-and-restore path
  under real conditions. Both involve an actuation that only a session or a
  hazard procedure can produce.

### `session_limit_seconds` Is Not Derivable Yet

The two rehearsals together measure the fixed overhead and the dominant
operator-paced term, and they leave the tail unmeasured. A ceiling assembled
from measured terms plus an estimate for the rest is an authority bound with a
guess in it, and the guess errs in the unsafe direction: too large a ceiling
authorises more autonomous exposure than anything measured, while too small a
one makes the instrument unusable and invites a request to raise it.

So this document does not claim the ceiling is derivable from rehearsals. Two
routes are open, and choosing between them is a review decision rather than a
drafting one:

- **Set it from measured terms with a stated, reviewed allowance for the tail**,
  recorded as residual and revisited after the first real sessions. This
  unblocks the fixture at the cost of one accepted estimate.
- **Hold it blocked until a reviewed hazard rig exists**, which can time the
  tail properly under [hazard-session.md](hazard-session.md). This keeps every
  bound measured and leaves the fixture unimplementable for longer.

Until one is chosen, `session_limit_seconds` has no ceiling and stage 0 keeps
refusing every fixture, which is the correct state rather than a stalled one.

## What This Procedure Does Not Establish

- **Nothing about the platform's failure modes.** Process loss, kernel failure
  and controller reset are properties of the actuator platform, established
  once per platform under a contract that does not yet exist
  ([ori-specs#158](https://github.com/ori-platform/ori-specs/issues/158)). A
  session that ran cleanly says nothing about what the output does when the
  runtime dies mid-window, and the fixture contract's interrupted-session rules
  exist precisely because it does not know.
- **Nothing about supervision.** A session records the supervision posture at
  open, including `unsupervised`. It does not create coverage.
- **No protection claim, of any strength.** Every surface reports supervised
  qualification with protection unavailable, throughout and afterwards. A
  session that trips exactly as specified has produced evidence toward a
  ratification review and has not protected anything, and no recording,
  screenshot, or demonstration of one may be captioned otherwise.

## Open Questions

- **An in-force binding has no legitimate actuation path outside a profile.**
  The proof operation is provisional-only; arming requires an active pair,
  which requires a ratified profile or a fixture. So a zone that has been
  proven and brought into force cannot have its actuator exercised at all until
  something grants Tier D authority over it. That is a defensible posture — it
  is the absence of an ungoverned actuation path, which is the thing this estate
  refuses everywhere — and it is also why the rehearsals above are non-actuating
  and why the tail cannot be timed. Whether a narrowly bounded, non-Tier-D
  maintenance actuation belongs on an in-force zone is a real question and a
  new authority; it is not answered here, and it must not be answered by
  loosening either existing lifecycle.
- **Hazard sessions** are specified in [hazard-session.md](hazard-session.md).
  That document is itself unreviewed, and no hazard session may be run until a
  competent person has accepted it for the installation in question.
- **Multi-circuit campaigns.** Dossier argument 6 wants distinct capacities,
  load classes and terminal states. Whether that means several fixtures against
  several bindings on one device, or several devices, changes what the ceilings
  bound, and is settled with the ceilings.
- **Who supervises.** This document requires a person with independent
  isolation who is not driving the console, which implies two people for a
  conforming session. Whether that is required or recommended is a decision the
  first campaign will make in practice and should make deliberately.
