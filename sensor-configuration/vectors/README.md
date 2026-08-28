# `sensor-configuration/v1` vectors

Five files, split by what a failure would blame.

| File | Blames |
| --- | --- |
| `protocol-definition.json` | An adapter registered without a declaration |
| `schema-load.json` | A declaration an adapter ships |
| `sensor-entry.json` | The canonical envelope an operator writes |
| `protocol-config.json` | A protocol configuration document, and its calibration siblings |
| `calibration.json` | A `(protocol, type)` calibration block |

A schema is checked on its own terms before any document is validated, so a
malformed declaration surfaces without a document in hand and never reaches the
operator as though they had written something wrong.

## Reading a case

`expect` is `accepted` or `refused`.

For an accepted case, `resolved` is the exact mapping the adapter must receive,
with defaults, fallbacks and aliases applied. Returning the input unchanged is
not acceptance: a validator that checks values but does not resolve them leaves
the adapter to apply its own defaults, which is the state this contract ends.

For a refused case, `must_name` lists the identifiers a conforming refusal must
contain — keys, paths, protocols and schema keywords. Wording is not specified;
naming the offending declaration is, so no case asks for an English phrase.

`must_name_at_load` lists identifiers the surrounding loader must add, which a
validator checking one document against one schema cannot know. The protocol is
the usual case: the loader chose the schema, so only the loader can say which
one refused. Requiring it of the inner layer makes a conforming implementation
look otherwise.

`warns` lists what a deprecation warning must name.

`external` is the rest of the device configuration, against which
`fallback_from` and `must_be_subset_of` resolve.

## The rule this corpus exists to enforce

**Producer and verifier MUST NOT share code.** A corpus checked only by the tool
that wrote it certifies that tool's defects. These files are hand-authored
beside the contract rather than generated from any implementation, and an
implementation claiming conformance must read them as data.

Three implementations interpreting one format separately is what this replaces.

## What is not here

**One accepted sensor per protocol.** The contract deliberately places schemas
in the adapter modules, so the fifteen real declarations do not live beside this
document and a corpus here cannot carry them without becoming the second copy
the contract forbids. That case is a conformance check an implementation runs
against its own definitions.

**An infinity as a value.** `Infinity` is not JSON. The one case that needs it
carries the declaration as `schema_text` instead, so a strict reader is not
taken down by a single case.
