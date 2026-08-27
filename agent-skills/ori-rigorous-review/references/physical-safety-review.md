# Physical Safety And Authority Review

Load this when a change can reach physical authority. The main skill carries the
trigger test; this file carries the rules.

Ori is an agent that takes physical actions. The Action Tier Framework decides
which actions fire autonomously, which need operator approval, and which cannot
be overridden. A review that treats those boundaries as ordinary code review is
the failure mode this document exists to prevent.

## The invariants

**Tier D is owned by the reviewed runtime release.** It is deterministic, fires
from the rule engine before any model, and is never conferred by a skill, a
model output, a policy entitlement, a cloud service, or a remote command. A
signature proves who wrote a document, not that they were entitled to write its
contents.

**Tier D is never gated.** Not by entitlement, licence, subscription, or payment
state; not by evidence availability; not by connectivity to a gateway or cloud.
Safety is core, not an upsell, and a protection claim that lapses when a
subscription does is not a protection claim.

**Deployment posture must be honest.** Phone-only deployments can detect,
reason, and alert. Autonomous physical protection requires a commissioned,
authenticated Edge Node and hardware-in-the-loop proof. When that node is
unreachable, the product reports protection *unavailable* — it does not fall
back to monitoring while keeping the claim.

**Nothing derives a circuit outcome from a contact type.** Not from NC/NO, not
from a relay label, not from `active_high`, not as a default, a fallback, or a
consistency check. What a load does when a coil de-energises is a property of
the downstream wiring, established per channel by commissioning: de-energise the
coil, observe what the load actually does, record it.

Require three independently commissioned facts per channel, none derived from
another:

```text
open_protected_circuit      -> energised | de_energised
close_protected_circuit     -> energised | de_energised
de_energised_terminal_state -> open | closed
```

Consistency between them may be *checked* — a self-contradictory set means the
commissioning test did not establish what it recorded. One may never be
*derived* from another.

**Controller-loss state is observed, not chosen.** Losing the controller
de-energises the coil; that is physics, and software commands nothing during a
brownout or a power cut. So `de_energised_terminal_state` is a commissioned
hardware property recorded by observation — never a behaviour software claims
to implement, and never something a review credits to code.

A conforming mapping may record `de_energised_terminal_state: closed`, a load
that reconnects when the coil drops. That zone's safety case is then not made by
software at all: it needs mechanical latching, an independent interlock, or an
operating procedure. Call a circuit state safe only where that zone's
commissioned mapping and safety profile say so.

What software does own is startup. At initialisation the runtime must
**explicitly command the commissioned coil state** rather than leaving the
output at whatever the platform defaults to. That command is a coil state, not
a GPIO level: the level that achieves it depends on `active_high`, so an
implementation that writes a logical low and calls the coil de-energised has
derived a coil state from board polarity it never checked.

**Division of decision.** A model may propose typed actions. The runtime and the
Action Tier Framework decide permission. The commissioned capability decides
feasibility. Observation proves outcome. A change that lets any one of those
four answer another's question is a finding.

## What to reject

- A claim that conceals unavailable actuation, or reports healthy while a
  requested physical capability is simulated.
- Advisory monitoring described, priced, or documented as protection.
- A fail-closed boundary weakened for convenience, latency, or test ergonomics.
- A host-only or simulated test presented as physical proof.
- A safety parameter arriving through a channel signed by the wrong authority —
  a declared capacity multiplied by a release-owned constant is a trip point,
  so whoever supplies the capacity moves it.
- An unknown or unrecognised tier treated as informational.
- A default actuation that is uncommissioned, unauthorised, or unproven.
  Actuation is legitimate: Tier D fires autonomously by design, and a Tier B
  action may execute without approval where policy says so. What must be
  refused is a path that actuates *without* a commissioned mapping for that
  channel, without the tier authority to do it, or without a proof that the
  commanded outcome was ever observed. A Tier C safe default is the narrower
  case: it stands in for an action the operator declined or never answered, so
  it must not actuate.

## Proof levels

State the highest level genuinely reached, and the boundary beyond it:

proposed → implemented → host-tested → target-built → HIL-proven →
manufactured/pilot-observed → production/commercially validated

A binding, mapping, or profile that is representable, signed, and
conformance-tested but read by no production consumer is at *implemented*, not
at *proven*. Say so plainly, and name where enforcement will live.
