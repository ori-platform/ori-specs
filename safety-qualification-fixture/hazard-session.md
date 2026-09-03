# Hazard Session Procedure

> Status: Design target, pre-ratification. Companion to
> [procedure.md](procedure.md), which covers the mechanism sessions a bench can
> run; this document covers the sessions that deliberately produce an
> over-condition. No hazard session has been run, and none may be until this
> document has been reviewed and accepted by a competent person for the
> specific installation it will be run on. Amendable in place under the
> [pre-ratification exception](../VERSIONING.md#pre-ratification-exception).

A mechanism session establishes that a reading reaches a commanded outcome. It
cannot establish that the trip point protects anything, because nothing was at
risk. Dossier
[argument 3](../safety-profile/ratification.md#3-trip-point) — protection and
discrimination — needs sessions in which the quantity genuinely exceeds what
the circuit tolerates, and this document is the procedure for those.

It is also the route by which
[`session_limit_seconds`](procedure.md#session_limit_seconds-is-not-derivable-yet)
becomes derivable from measurement rather than allowance, since it is the only
procedure that times a real trip-and-observe tail.

## Two Sub-Classes, And Most Of The Evidence Is In The Safe One

Argument 3 has two halves, and they carry very different risk.

| Sub-class | What is done | Hazard | Establishes |
| --- | --- | --- | --- |
| **Discrimination run** | The real load is operated normally and repeatedly — cold starts, inrush, duty cycling, worst-case simultaneous starts — with the pair armed and evaluating | None beyond ordinary operation of that load | That the trip point sits **above** what the healthy load does. Every run that does not trip is the evidence |
| **Protection run** | The current is deliberately driven above the circuit's continuous rating, under controlled conditions, until the pair trips | Real: thermal, arc, and equipment damage | That the trip point sits **below** the level at which the circuit is damaged, and how long the cutoff takes |

**Run every discrimination run first, and expect them to carry most of
argument 3.** They need no laboratory, no fault energy, and no special source —
only the real load, the real sensing path, and an open window. A trip point
that nuisance-trips is discovered here, cheaply, before anyone builds a
protection rig. A campaign that starts with protection runs has taken the
larger risk to answer the smaller question.

A discrimination run is procedurally a session under
[procedure.md](procedure.md) with step 7 replaced: instead of approaching the
trip point, the operator exercises the load's normal extremes and records that
nothing fired. Its preconditions are that document's; this one adds none.

Everything below concerns protection runs.

## Where A Protection Run May Be Conducted

**Not on the bench, and not on a live installation.** A protection run needs a
circuit built for it.

- **A current-limited source.** The supply MUST be incapable of delivering more
  than a stated bound — a laboratory supply, a variac into a resistive load
  bank, or an equivalent — and that bound MUST be recorded. Real prospective
  fault current is not required to answer argument 3: the question is whether a
  cutoff fires below the damage threshold and how fast, and a controlled ramp
  to a known current answers it without arc energy nobody needs to produce.
- **Independent overcurrent protection, upstream, that is not Ori.** A device
  whose characteristic is known and whose let-through the circuit tolerates.
  **Ori is the device under test and MUST NOT be the only thing between the
  source and the hazard.** If the pair fails to trip, this device is what ends
  the run.
- **A sacrificial or rated-for-purpose protected circuit.** The elements that
  define `rated_capacity_amps` are being driven past their continuous rating.
  Either they tolerate it for the run's duration with margin, established
  beforehand, or they are consumables.
- **Containment and separation.** Nobody within reach of the circuit while
  current is above rating. Remote or barriered operation of the source.
- **Independent instrumentation.** See below.

## The Measurement Under Test Cannot Be The Only Witness

**A reference instrument, independent of the runtime's sensing path, MUST
record the current throughout every protection run** — a calibrated clamp
meter with logging, or a current probe on a scope.

The runtime's own path is what
[argument 2](../safety-profile/ratification.md#2-measurement) is trying to
validate. Using it as the sole record of what the circuit carried would settle
argument 3 with evidence that argument 2 has not yet accepted, and a
calibration error would appear as a correct trip at the wrong current with
nothing to contradict it.

The reference record and the runtime's readings are retained together. Their
disagreement is itself a result, and is evidence for argument 2 as well as
argument 3.

## Authority Is Unchanged

**A protection run is a qualification session and nothing more.** It runs under
an accepted fixture, inside an open window, with the same interactive local
consent, the same durable session consumption, the same monotonic bound, and
the same stage-0 refusal while the exposure ceilings are unratified. This
document adds preconditions; it adds no authority and relaxes none.

In particular it does not permit a candidate to evaluate outside a session, and
it does not make the fixture's ceilings negotiable for a run that wants longer.

## The Run

Preconditions are [procedure.md](procedure.md#preconditions) in full, plus the
circuit requirements above, plus:

- the review named at the head of this document, accepted for **this**
  installation, retained with the record;
- the damage threshold of the protected circuit's limiting element, stated with
  its basis, and the maximum current and duration the run will not exceed;
- the upstream protective device's characteristic, and confirmation that its
  let-through is tolerable for the circuit;
- a stated maximum number of consecutive runs before the circuit is inspected,
  since repeated over-rating accumulates thermal damage that no single run
  shows.

Then, replacing steps 7 and 8 of [procedure.md](procedure.md#the-session), and
adding one:

**Step 7 — Ramp.** Raise the current toward the trip point in stated
increments, holding at each, with both the reference instrument and the runtime
recording. Hold below the trip point at least once — a negative run — and
confirm nothing fires.

**Step 8 — Cross and observe.** Continue past the trip point. Record, from the
reference instrument, the current at which the circuit actually opened and the
elapsed time from crossing to interruption; and from the runtime, the reading
that satisfied the condition, the verdict, `intent_append_ms`,
`command_issue_ms`, the command status, and the observed effect.

**Step 9 — Inspect.** Before any further run, inspect the limiting element for
thermal damage and record the result. This step is additional: the ordinary
procedure has no equivalent, because a mechanism session does not drive a
circuit past its rating.

**If the pair does not trip**, the upstream device or the source's own limit
ends the run. That is a **failed protection run**, it is recorded as one, and
it is among the most informative results the campaign can produce. It MUST NOT
be re-attempted by widening the window, raising the current beyond the stated
maximum, or adjusting anything the fixture governs.

## What A Protection Run Does And Does Not Establish

**Does:** that a cutoff fired, at a current the reference instrument measured,
within a time it measured, below a stated damage threshold, on this circuit.
Repeated across capacities and load classes, that is
[argument 6](../safety-profile/ratification.md#6-coverage)'s input as well.

**Does not:**

- **Behaviour at real prospective fault current.** A current-limited ramp is
  not a short circuit. Whether the switching device interrupts a genuine fault
  is a property of that device and its own rating, established by its
  manufacturer, not by this procedure. A dossier MUST NOT read a successful
  controlled run as evidence of fault interruption.
- **Anything about the platform's failure modes**, which are
  [ori-specs#158](https://github.com/ori-platform/ori-specs/issues/158)'s.
- **A protection claim for the deployment.** Every reporting rule of the
  fixture contract holds throughout: supervised qualification, protection
  unavailable.

## Contribution To The Exposure Ceilings

This procedure is the second of the two routes in
[procedure.md](procedure.md#session_limit_seconds-is-not-derivable-yet). A
protection run measures the trip-and-observe tail and the abort-and-restore
path that no non-actuating rehearsal reaches, so `session_limit_seconds` can be
set from measurement across the whole window rather than from a measured
prefix and an allowance.

The cost is ordering: the ceilings then wait on a reviewed hazard rig, and the
fixture stays refused at stage 0 until it exists. Choosing between that and an
allowance-based ceiling is the review decision the other document names, and
this one exists so the choice is between two specified options rather than
between one option and an absence.

## Open Questions

- **Who reviews, and against what standard.** This document requires acceptance
  by a competent person for a specific installation and does not say who is
  competent or what standard they apply. That is an organisational and
  regulatory question, and it is the first thing to settle before a rig is
  built.
- **Damage-threshold basis.** The run is bounded by a stated damage threshold
  for the limiting element. Where that number comes from — a datasheet, a
  standard's table, a thermal calculation — is unspecified, and it is the
  number the whole run is designed around.
- **Repeat limits and cumulative damage.** A maximum consecutive-run count is
  required and no basis for choosing it is given. Cumulative thermal ageing is
  real and invisible between runs.
- **Whether a discrimination-only campaign can support a first ratification.**
  If discrimination runs close their half of argument 3 and the protection half
  rests on the switching device's own rating plus a calculation rather than a
  run, a first ratification might be defensible with no protection run at all.
  That would be a narrower ratification, and it is worth deciding deliberately
  rather than by default.
