# Runtime Configuration Surface Inventory

> **Not a contract.** Not versioned, not in the baseline, not implemented
> against. This is the evidence used to design `runtime-config/v2`, and it is
> here to be checked rather than believed.

## Why it exists

A first attempt at `runtime-config/v2` was written from `runtime-config/v1.md`
plus a reading of `ori.yaml.example`. Applying its own machine schema to that
example produced **35 unknown paths**, 34 of them shipped features — gateway
custody, firmware telemetry and commands, persistent replay protection, serial
`port` and `baud_rate`, reasoning escalation and causal memory, firmware
provisioning credentials, six lockout thresholds, local-console approval, state
compaction, logging rotation and audit flags.

A conforming runtime under that schema would have refused the configuration
this repository publishes.

The defect was the method, not the rows. So the surface is now **extracted**
rather than transcribed, and the extraction is the artifact.

## Method

`runtime-surface-inventory.json` is generated from `ori/config.py` at
`ori-runtime` commit
**`e0d17f61128658b114a2473461ef551c2560a193`**.

**177 configuration paths across 19 parse functions**, 39 required.

### Receivers are tracked, because a key is not a path

A first attempt recorded every `.get()` and subscript inside a parse function,
whatever object it was called on. That counts **dictionary accesses**, not
configuration paths: signature blocks, environment lookups and local
bookkeeping were flattened in beside real settings, so `verified`,
`signed_at_ms` and `required` appeared as though an operator could set them.
That inflated the count to 209.

A second version tracked receivers but not loops, so `sensors` and `skills` are
parsed by iterating and **every key beneath them vanished** while the total
stayed confident at 169. Both versions were plausible, reproducible and
semantically false, which is why the extractor now carries regression tests in
`ori-runtime` at `tests/test_config_surface_inventory.py`.

The walk now carries an environment mapping local names to the configuration
path they hold. A read is recorded only when its receiver is in that
environment, and a sub-section binding extends the path rather than starting a
new one. **A key nobody can trace back to the parsed document is not a
configuration key.**

### What is mechanical, and what is not

Mechanical: the path, the function that reads it, whether it is required, its
literal default, and whether the example sets **that exact path** — suffix
matching credited every `enabled`, `port` and `mode` to whichever section
happened to contain one.

**Marked `review` rather than guessed:** types, constraints, conditional
dependencies, **disposition, authority and destination**. The first three live
in validation code. The last three are decisions, and defaulting 176 entries to
`retain` under `provisioning` would assert both while claiming to refuse
guesses.

## Sensors are open by construction

This is the finding that most changes the v2 design.

`_parse_sensors` keeps five keys — `id`, `type`, `protocol`,
`poll_interval_ms`, `calibration` — and routes **every other key into
`metadata`**, validated only when `protocol` is `coap`. `runtime.py` then
merges `metadata` into the dict handed to `adapter.connect()`.

So the accepted sensor surface is not one shape. It is **whatever each adapter
reads**, and there is no specification of it anywhere. `adapter_metadata` in
the JSON is that surface, extracted the same way: **17 adapter classes, 37
distinct metadata keys**.

A closed `Sensor` object cannot represent this. It needs discriminated variants
per protocol, and those variants have to come from the adapters rather than
from prose.

### A defect this surfaced

`ori.yaml.example` configures a `protocol: serial` sensor with
`baud_rate: 9600`. `SerialAdapter` reads `config.get("baudrate", 9600)`.

**The key is silently ignored.** It works today only because the example's
value equals the adapter's default. Change the example to `19200` and the
device runs at 9600 with nothing reported.

`UsbSerialAdapter` reads *both* `baud_rate` and `baudrate`, which is how the
inconsistency survived.

This is precisely the argument for closing the surface — a key the runtime
discards reads, to whoever wrote it, as a setting that took effect — and it was
found in the shipped example rather than hypothesised.

## Dispositions

Every entry carries `disposition`, `authority` and `destination`.

Every entry is `review` except one.

**The single asserted relocation:**

| Path | To | Authority |
| --- | --- | --- |
| `device.rated_capacity_amps` | A commissioned zone's `rated_capacity` | commissioning |

It reads as inventory and is not: a release-owned multiplier turns it into a
trip point, so whoever supplies it decides when a cutoff fires.

**Everything else is `review`** — not `retain`. Marking 176 entries retained is
a disposition decision and assigning them all to `provisioning` is an authority
decision, neither of which this artifact has standing to make. That is what the
review is for.

## What the inventory does not cover, stated rather than implied

The walk covers `ori/config.py`'s parse functions. It does **not** cover keys
read elsewhere: `runtime.py` assembles the dict handed to `adapter.connect()`,
and adapters read from it directly.

Of the shipped example's leaf paths, **80 are not parsed by `config.py`**.

**Adapter correlation is applied to `sensors[]` only.** An earlier version
matched any path whose final segment appeared in any adapter, which credited
`actions.coap.timeout_s` and `actions.sms.gsm.port` to sensor adapters that
merely read `timeout_s` and `port`. Sharing a leaf name with an unrelated
adapter is not evidence of anything.

| Sensor path | Read by |
| --- | --- |
| `sensors[].address` | `I2CAdapter` |
| `sensors[].channel` | `I2CAdapter` |
| `sensors[].port` | `GrowattAdapter`, `SerialAdapter`, `SolarmanModbusAdapter` |
| `sensors[].baud_rate` | **`UsbSerialAdapter` only** |
| `sensors[].calibration.*` | nested block, not top-level metadata |

`baud_rate` is the defect in `ori-runtime` #411, shown mechanically: the
example's **serial** sensor sets a key only the **USB** adapter reads.

The `calibration` rows carry `nested_block` because `runtime.py` passes
calibration as its own block rather than merging it into metadata, so its inner
keys never appear as top-level adapter reads. An empty reader list there is
expected, not a finding.

Outside `sensors[]` no adapter claim is made. The only mechanical statement left
is whether a leaf key name appears in `ori/` at all — **evidence of probable
dead configuration, not proof**, since a key could be assembled dynamically:

| Path | |
| --- | --- |
| `actions.relay.active_high` | Name absent from `ori/`; the actuator polarity defect in `ori-runtime` #397 |
| `skills[].config.energy_cost_naira` | Skill config, consumed by the skill |
| `skills[].config.owner_name` | Skill config, consumed by the skill |
| `skills[].config.requires_approval_for_soft_actions` | Skill config, consumed by the skill |

## What this forces a decision on

Two things cannot be settled by inventory, and v2 cannot be written until they
are:

**Sensor variants.** Per-protocol schemas derived from the 17 adapters, or
delegation to per-adapter contracts. Either way the adapter metadata surface
becomes specified for the first time.

**Skill configuration.** `skills-package/v2` defines `config` as an arbitrary
object with reserved-name checks and **no per-skill schema**. The withdrawn v2
draft claimed delegation to a schema that does not exist, and its corpus
invented a `skill_manifests` table so the vector would pass. A real target has
to exist before v2 can delegate to one.

## How to check this artifact

The generator lives in `ori-runtime` at
`tests/golden/build_config_surface_inventory.py`, because it inspects that
repository's source and this one is documentation-only. It is present **in the
pinned commit**, so the command below works from a clean checkout — an earlier
draft pinned a commit the generator did not yet exist in, which made the
documented reproduction impossible. Run it from an `ori-runtime` checkout at
that commit:

```bash
python3 tests/golden/build_config_surface_inventory.py > runtime-surface-inventory.json
```

It reproduces the committed file byte-for-byte. The generator is the tool, not
the authority; the committed JSON is. Anything in this README that the JSON
does not support is a bug in the README.
