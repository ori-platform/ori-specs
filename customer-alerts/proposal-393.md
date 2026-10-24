# Customer alert semantics: discussion draft for #393

This is a **proposal**, not a ratified contract or evidence of a working
detector. It records the minimum shared meaning needed before Ori Energy #92
and ori-runtime #528 can claim that all four customer switches work. The
shipped `device-policy/v1` baseline is frozen; this draft does not amend it.

## Scope and ownership

The product owns the customer wording, preference authorization and signed
policy production. The runtime owns sensing, detection, local alert transport
and durable attempt reporting. A customer preference controls only optional
notifications. It does not authorize, block, delay or reclassify a physical
action; it cannot disable a mandatory protection-degraded notice. A provider
accepting a send is not proof that a handset received it.

The policy extension or successor contract must say that each absent known
`alerts` key is enabled, and that unknown keys have no effect. Only a verified,
current policy with an explicit `false` for a known class may suppress its
optional notice. A stale or invalid policy cannot introduce a new suppression.
The on-wire version and canonical-signature consequences must be checked
against shipped policy consumers before that rule becomes normative.

## Proposed class meanings

| Class | Positive evidence required | Must not be inferred from | Current runtime evidence |
| --- | --- | --- | --- |
| `generator_running_too_long` | A confirmed generator-running state that remains continuous for at least two hours **after** confirmed grid restoration. The clock must survive or explicitly reset after runtime restart; missing state cannot count as running. | High load, an LLM diagnosis, grid voltage alone, or a requested source-transfer action. | No matching detector. The prosumer skill reads generator power, but its declared generator-power inputs are simulations; that is not yet proof of a commissioned generator-running sensor. `sustained_overdraw` measures load. |
| `unusual_consumption_spike` | A fresh, quality-qualified consumption measurement significantly above a valid site baseline, with context-aware exclusion of known expected changes. The contract must name the physical quantity, baseline window and threshold meaning. | An arbitrary current spike without a valid baseline or a prompt example. | `energy-anomaly-detector/sudden_load_spike` is a candidate Tier A trigger and is bound on an unmerged runtime branch. Its thresholds and end-to-end proof still need review. |
| `battery_underperforming` | Measured charge input and measured later battery output over a comparable, complete interval or cycle, plus a commissioned expected-performance model and justified tolerance. The product must decide whether this is the intended meaning; without those measurements, the switch cannot honestly claim this alert. | Low state of charge alone, lack of charging under sun alone, or the Tier D battery cutoff. | The prosumer skill integrates battery charge and discharge power from inverter telemetry into a ledger, but no existing alert compares a completed interval with a commissioned performance model. Other battery rules detect different conditions. |
| `grid_power_restored` | A transition from confirmed unavailable grid power to stable available grid power at the **grid input**, with freshness, debounce and a new-event reset. It is an observational Tier A notice independent of source-transfer command success. | One voltage sample, a generator-load assumption, or the Tier B `grid_phcn_restored` rule. | The prosumer skill reads grid-availability and voltage signals for advisory calculations, but no general restoration detector exists. Some declared grid-availability inputs are simulation-only. |

The two-hour generator duration is current Ori Energy UI copy. The other
thresholds, debounce windows and model tolerances are **not yet agreed**;
inventing numbers here would make a purported contract look more certain than
the source evidence warrants. Each class needs explicit behavior for stale,
missing, implausible and conflicting readings. Such a state means the detector
is unavailable or uncertain, never that the positive condition was observed.
Health reporting must distinguish an unavailable detector from a user-disabled
alert so the UI does not represent either as protection being switched off.

## Trigger binding and outcomes

The runtime binding is keyed on a reviewed first-party skill identity and
trigger identity, rather than a display label or trigger name alone. A
community skill that happens to use the same trigger name cannot inherit the
customer preference. One logical alert may have SMS and WhatsApp attempts
with independent outcomes. A `delivery_attempt_id` identifies one provider
attempt; replaying its report after a lost API acknowledgement reuses that ID,
while a new provider call gets a new ID. `accepted` means provider acceptance;
handset delivery remains unknown without a separate receipt contract.

## Decisions required before a normative version

1. Confirm the intended battery meaning with Product and the available device
   measurements. Decide whether to revise the customer copy if the required
   input/output comparison cannot be supported.
2. Name the authoritative generator-running and grid-input sensors and their
   provenance, including sites that have no generator or grid sensor.
3. Specify each detector's baseline, threshold, dwell/debounce, cooldown,
   restart persistence and reset behavior. Provide traces that exercise both
   positive and rejected cases.
4. Choose a compatible policy extension or a successor to frozen
   `device-policy/v1`. Define whether the alert-condition contract is a
   separate versioned domain and where its vectors live.
5. Decide whether push/local notification is a separate contract in this
   increment; do not collapse push, SMS and WhatsApp into one delivery status.
6. Inventory current producers and consumers before changing signed policy
   bytes or validation. Keep HIL and live-provider proof explicitly pending.

Related: [ori-specs #393](https://github.com/ori-platform/ori-specs/issues/393),
ori-energy #92 (private repository), and
[ori-runtime #528](https://github.com/ori-platform/ori-runtime/issues/528).
