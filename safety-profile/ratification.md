# Safety Profile Ratification

> Status: Design target, pre-ratification. Companion to
> [safety-profile/v1.md](v1.md), which owns what a profile is; this document
> owns what licenses one to become `ratified`. No profile has been ratified
> under it. Amendable in place under the
> [pre-ratification exception](../VERSIONING.md#pre-ratification-exception).

[safety-profile/v1.md](v1.md) fixes that ratification is a contract change,
that it flips `status` in `profiles.json`, that it moves every consumer's
pinned digest, and that it is made "only when the questions recorded for that
profile under Open Questions are closed". It does not say what closing one
looks like, what evidence closes it, who decides, or what is retained
afterwards. This document says those four things.

It exists because the alternative is a ratification made the way software
decisions are usually made — a reviewer reads a diff flipping a string from
`candidate` to `ratified`, sees a passing corpus, and approves. The corpus
proves the number is spelled correctly. Nothing in a repository proves it is
safe, and nothing in a diff shows what was never established.

## What Ratification Is

**Ratification grants a number the authority to open a circuit without an
operator, on every device that holds the release, for as long as that profile
ships.** It is the widest grant in the platform: broader than a commissioned
binding, which covers one site; broader than a capability grant, which is
capped at Tier C; broader than a qualification fixture, which covers one pair
on one device until it expires.

It is therefore not:

- an installer approval, a configuration switch, a deployment-profile setting,
  a DevicePolicy field, an entitlement, or a remote command;
- a successful bench trip. A profile that fired correctly once has been shown
  to work on one commissioned setup;
- a passing vector corpus. The corpus proves an implementation agrees with the
  contract, and the contract is what is under review;
- a maintainer's judgement recorded nowhere. The dossier is the artifact.

## The Dossier

A profile is ratified against a **dossier**: a retained document holding six
arguments and the evidence each rests on. A ratification review reads the
dossier, not the diff. An argument is **open**, **supported**, or **closed**,
and a profile may be ratified only when all six are closed.

| # | Argument | Closes when |
| ---: | --- | --- |
| 1 | Quantity | The condition's inputs are identified as specific physical quantities, and the one that limits the circuit is the one the condition uses |
| 2 | Measurement | The sensing path is shown to report that quantity, in that unit, to an accuracy and over a window the condition needs |
| 3 | Trip point | The number, and the characteristic it is applied through, are shown to protect the hazard without nuisance-tripping the load |
| 4 | Latency | Total decision-to-command latency, measured, is shown tolerable for the hazard |
| 5 | Failure modes | The consequences of a missed trip, a spurious trip, and an unexecutable trip are stated and accepted for the zone population |
| 6 | Coverage | The population of zones the profile will activate on is identified, and the arguments above are shown to hold across it — not only where it was tested |

Each argument names its evidence, and each piece of evidence carries the proof
level it actually reached:

```text
proposed → implemented → host-tested → target-built → HIL-proven →
manufactured/pilot-observed → production/commercially validated
```

An argument closed on `host-tested` evidence where the claim is physical is not
closed. Saying so in the dossier is the point of recording the level.

### 1. Quantity

**Every input the condition reads must be named as a physical quantity, and the
one that limits the circuit must be the one the condition is applied to.**

For a `upper_capacity_multiplier` condition this is the whole argument, because
the trip point is a site-supplied capacity multiplied by a release-owned
constant. A nameplate rating, a conductor ampacity, a breaker rating, a
contactor rating and a permitted continuous current are five different
quantities with different margins above them, and a multiplier chosen against
one of them is wrong against the others.

The commissioned binding records `rated_capacity.provenance` — `nameplate`,
`installer_measured`, `design_document` — which says what kind of claim was
made and **not which rating limits the circuit**.

A dossier that says "capacity means the rated capacity" has restated the field
name and closed nothing.

#### Decided for `rated_capacity_amps`

Of the two available resolutions — constraining the profile to admissible
provenances, or pinning the semantic in the contract that owns the field — the
second was taken, because provenance describes how a claim was established and
no constraint on it can say *which quantity* was claimed.

**`rated_capacity_amps` is the continuous current rating of the most limiting
series element of the protected circuit**, defined in
[commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md#what-rated_capacity_amps-is),
with a required `limiting_element` naming which element the value came from.
Explicitly excluded: the load's nameplate draw, the supply's capacity, and any
upstream protective device's rating — the last of which belongs to argument 3,
since it is a question about coordinating a trip point rather than about
identifying a quantity.

The `limiting_element` field is what makes the definition enforceable rather
than merely stated. Without it, a document carrying a load's nameplate draw is
indistinguishable from one carrying a conductor's ampacity, and the error
surfaces after commissioning rather than while someone can still see the
wiring.

**Argument 1 is therefore `supported`, not `closed`, for this profile.** The
definition is fixed and the forcing function is specified, but the amendment
that carries them is unmerged and its corpus does not yet exercise the field.
An argument closed on an unmerged amendment is not closed. It becomes `closed`
when the binding amendment lands with a regenerated corpus and consumers
re-vendor.

### 2. Measurement

**The evaluator compares against whatever the adapter reports.** Closing this
argument requires showing, on the target platform, that the reported value is
the named quantity in the named unit, and that its accuracy and measurement
window are adequate for the condition.

The window is not a detail. An instantaneous sample and an RMS value over a
mains cycle are different quantities that share a unit, and a multiplier
applied to the wrong one either misses a real fault or trips on a peak that
every conforming load produces. A dossier must state which the condition
consumes and show that the adapter produces it.

Where a runtime defect is the obstacle, the dossier names it and the argument
stays open until the defect is fixed and the fix is proven on the target — not
on a host.

### 3. Trip Point

**A number reproducing a legacy trigger has been shown equivalent to it, not
shown safe.** Closing this argument requires an affirmative case: why this trip
point, applied through this characteristic, protects the hazard.

Two halves, and both are needed:

- **Protection.** The trip point is below the level at which the hazard does
  damage, with enough margin for the measurement accuracy from argument 2 and
  the latency from argument 4.
- **Discrimination.** The trip point is above what the healthy load actually
  does, including start-up transients, inrush, duty-cycle peaks, and any
  upstream device that shares the circuit. A cutoff that nuisance-trips is
  removed by whoever it inconveniences, and a removed cutoff protects nothing.

Where the two cannot be separated by a single instantaneous threshold, the
argument closes only by adopting a time-current characteristic — which is a new
condition kind, a change to [safety-profile/v1.md](v1.md), a change to the
profile set, and a change to every consumer's pinned digest. Discovering that
during ratification is a normal outcome, not a failure of the dossier.

### 4. Latency

**Measured, on the target, through the real path.** The quantity is the total
elapsed time from the credible reading that satisfies the condition to the
driver accepting the command, and it includes the bounded pre-command
trip-intent append that [safety-profile/v1.md](v1.md#trip-state) requires.

The contract caps that append's deadline and explicitly refuses to call the cap
a safety argument: a profile's ratification must demonstrate the declared
deadline is tolerable for that profile's hazard, and a profile whose hazard
cannot tolerate it cannot be ratified for consumers declaring it. That
demonstration lives here.

Latency evidence comes from qualification sessions under
[safety-qualification-fixture/v1.md](../safety-qualification-fixture/v1.md),
which requires every session to record `intent_append_ms` and
`command_issue_ms` for exactly this reason. A dossier quoting a design target
rather than a measured distribution has not closed this argument.

### 5. Failure Modes

Three questions, answered for the zone population rather than for the bench:

- **Missed trip.** The condition held and nothing fired — a sensor fault, a
  measurement-loss window, a refused reading, a trip that could not be
  executed. What the hazard does in that interval, and what else in the
  installation is expected to catch it.
- **Spurious trip.** The condition did not hold and the circuit opened anyway.
  What losing the load costs, and whether anything downstream is damaged by an
  unplanned interruption. "A spurious trip is safe and annoying" is the
  contract's default posture and it is a claim about the load, so a dossier
  states it for this profile's loads rather than inheriting it.
- **Unexecutable trip.** The outcome was decided and the actuator refused,
  was unreachable, or was never verified to have moved. The runtime reports
  such a zone as unprotected and keeps retrying; the dossier states what that
  means for the hazard while it persists.

The zone's commissioned `de_energised_terminal_state` belongs to this argument.
A profile ratified for zones whose de-energised state is `closed` is a profile
whose protection depends on the controller staying alive, and that dependency
is stated here or the profile is constrained to `open`-terminal zones.

### 6. Coverage

**A profile activates on every eligible zone on every device holding the
release.** The dossier identifies that population — the sensor types, capacity
range, load classes, and terminal states it will reach — and shows the five
arguments above hold across it.

This is the argument a bench cannot close by itself, and the reason the
sufficiency rules below exist. Where the arguments hold only for part of the
population, the profile is constrained rather than ratified broadly:
constraining is expressible today through the activation checks (unit,
direction, capacity parameter, observable trip point) and, where those are not
enough, through a new activation check, which is a contract change.

## Sufficiency

**The dossier is complete when every argument is closed. It is never complete
because a number of sessions were run.** Sessions are evidence for arguments 2,
3, 4 and 5; they are not a currency that buys ratification.

That said, arguments 3, 4 and 6 need bench evidence, and how much is what the
qualification fixture's exposure ceilings are sized against. The rules:

- **A single circuit closes nothing about a population.** One commissioned
  setup, one capacity, one load class is evidence that the mechanism works, and
  it is argument 6's weakest possible input.
- **Diversity beats repetition.** Ten sessions on one bench circuit produce a
  latency distribution and nothing else. Sessions across distinct capacities,
  load classes and terminal states are what argument 6 consumes.
- **Negative sessions count.** A session in which the condition was approached
  but not crossed, and nothing fired, is evidence about discrimination. A
  procedure that only records trips measures only half of argument 3.
- **Non-bench evidence is admissible and often better.** A component datasheet,
  a conductor ampacity table, a published time-current curve, or a calculation
  closes parts of arguments 1 and 3 more strongly than any number of
  observations. The dossier prefers it where it exists.

**Sessions per argument** is therefore the quantity that sizes the fixture
ceilings, and it is derived per profile in its own dossier, not fixed here.

## Who Decides, And What Is Retained

A ratification is a **release-level decision**, reviewed by the CODEOWNERS of
this repository and of the consuming runtime, with at least one reviewer who
did not author the profile, the dossier, or the qualification records. An
automated review — of any provenance — is an input and never an authorisation.

The dossier is retained with the release that ratifies the profile, and it
records the decision: who reviewed, on what date, against which dossier
revision, and what was explicitly accepted as residual risk. A ratification
whose residual risks are not written down has not identified them.

**Ratification is revocable by release.** A profile returns to `candidate` in a
later release when an argument is reopened; consumers pick that up through the
pinned digest like any other profile-set change. There is no field, command, or
local mechanism that suspends a ratified profile on a device, and this document
introduces none — the removal path is a release, as the grant path is.

## The v1 Candidates

Every profile in the shipped set is a `candidate`, and none has a dossier. The
state of each argument, as of this document:

### `electrical.overcurrent.v1` — `rated_capacity_amps × 2.0`

| Argument | State | Blocker |
| --- | --- | --- |
| 1 Quantity | **Supported** | Semantic pinned to the most limiting series element's continuous rating, with a required `limiting_element`; the binding amendment and its regenerated corpus are unmerged |
| 2 Measurement | **Open** | `ads1115_current` reports an uncalibrated instantaneous sample as amperes ([ori-runtime#398](https://github.com/ori-platform/ori-runtime/issues/398)); calibration never reaches the adapter |
| 3 Trip point | **Open** | `2.0` was chosen for equivalence with the legacy trip point and observability on the documented sensor. Neither is a protection or discrimination argument. Whether an instantaneous characteristic suffices is undecided |
| 4 Latency | **Open** | No measured latency exists on the target. Requires qualification sessions, which require the fixture, which requires its ceilings |
| 5 Failure modes | **Open** | Not stated for any load class |
| 6 Coverage | **Open** | Population not identified |

Argument 2 is the true critical path: sessions run against an unvalidated
measurement produce latency numbers and no evidence about arguments 3 or 6,
because what was compared against the trip point was not the quantity the
condition names.

### `electrical.overvoltage.v1` — `260.0 V`

| Argument | State | Blocker |
| --- | --- | --- |
| 1 Quantity | **Open** | The threshold names no nominal voltage and no tolerance band. `260.0` means one thing on a 230 V nominal supply and another on a 240 V one |
| 2 Measurement | **Open** | Instantaneous versus RMS is unstated, and the two differ by a factor of √2 on a sinusoid — a distinction that decides whether this threshold is high or unreachable |
| 3–6 | **Open** | Not begun |

### `gas.concentration.v1` — `400.0 ppm`

| Argument | State | Blocker |
| --- | --- | --- |
| 1 Quantity | **Open** | The threshold names no gas. 400 ppm is roughly ambient for carbon dioxide and far above the alarm level for carbon monoxide |
| 2 Measurement | **Open** | Sensor class, cross-sensitivity and warm-up behaviour unstated |
| 3–6 | **Open** | Not begun |

### The two Tier D removals

[safety-profile/v1.md](v1.md#migration) migrates
`battery_emergency_cutoff` and `cpu_overheating` out of Tier D on the ground
that neither reaches an actuator, and records both as needing ratification
because removing `action_tier: D` changes what a shipping skill claims.

A removal dossier is shorter and asks one question: **does anything today
actuate on this trigger, and does anything downstream believe it does?** It
closes on evidence that no executor exists for the named actions, that no
deployment documentation promises the behaviour, and that the replacement tier
preserves the observation and the alert. It does not need arguments 1 through
6, because nothing is being granted.

## Relationship To Neighbouring Contracts

| Contract | Owns |
| --- | --- |
| This document | What licenses a profile to become `ratified`, and what is retained |
| [safety-profile/v1.md](v1.md) | What a profile is, its evaluation, activation, and trip state |
| [safety-qualification-fixture/v1.md](../safety-qualification-fixture/v1.md) | How a candidate is exercised on hardware before ratification |
| [safety-qualification-fixture/procedure.md](../safety-qualification-fixture/procedure.md) | How a session is run safely, and what it records |
| [commissioned-safety-binding/v1.md](../commissioned-safety-binding/v1.md) | The zone, its capacity, its mapping, and their proof |

## Open Questions

- **Whether the four `limiting_element` values are sufficient.** `conductor`,
  `termination`, `connector` and `switching_device` cover the series elements a
  commissioned zone is expected to contain. A site whose limiting element is
  none of them is refused rather than resolved, which is the house rule and is
  also a way to discover that the list is short. The first campaign against a
  real panel is what will show whether it is.
- **Whether a dossier is a public artifact.** It records residual risk accepted
  by named reviewers. Publishing it is the honest posture for an open runtime
  and is also a disclosure decision this document does not own.
- **Re-ratification cadence.** A dossier closed against one platform, one
  adapter implementation and one measurement path can be invalidated by a
  change to any of them. Whether ratification expires, or is re-examined on a
  triggering change, is unsettled. The trigger set is at least: a change to the
  condition kind's evaluator, to the sensing path, to the pre-command deadline,
  or to the actuation seam.
- **Sufficiency across a fleet.** Arguments 5 and 6 concern a population that,
  for a first ratification, does not exist yet. Whether a profile may be
  ratified for a deliberately narrow population and widened later — and what
  widening requires — is unsettled, and is the likeliest shape of a first
  ratification.
