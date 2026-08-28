# `skills-package/v3` vectors

Four files, split by what a failure would blame.

| File | Blames |
| --- | --- |
| `tier-authority.json` | A package reaching for authority v3 does not grant |
| `schema-load.json` | The `config_schema` a skill author wrote |
| `package-config.json` | The `config` beside that schema |
| `signature.json` | A manifest altered between signing and loading |

A schema is checked on its own terms before any `config` is considered, so a
malformed declaration surfaces without a document and is never reported as
though the configuration were at fault.

## Reading a case

`expect` is `accepted` or `refused`. `error` names the class a conforming loader
raises — `SkillValidationError` for a malformed package, `SkillSecurityError`
for provenance. The distinction is normative: **a Tier D declaration is a
validation error from every package**, because in v3 nobody holds that
authority, so provenance is not the question being asked.

For an accepted case, `resolved` is the exact mapping the skill must receive,
with declared defaults applied at every depth. Returning the input unchanged is
not acceptance — the hook would then apply its own defaults, and the schema
would describe nothing.

For a refused case, `must_name` lists identifiers the refusal must contain,
by full path where the key is nested. Wording is not specified; naming the
offending declaration is.

**Each case is valid in every respect except the one it names.** A vector
carrying a second defect lets a conforming implementation refuse for that
instead and never reach the boundary under test — which reports coverage that
does not exist.

`tier-authority.json` carries an `action_registry` fixture — minimum tiers for
the actions its cases name. The real registry is runtime-owned and outside this
contract; the fixture exists so a consumer with no registry of its own, an
authoring SDK or a catalogue, reaches the same verdict as a runtime instead of
importing runtime state. A case that needs it says so with `"registry":
"action_registry"`.

`signature.json` carries complete community manifests with real ed25519
signatures over the canonical bytes of [`signing/v1`](../../signing/v1.md),
made with the published interoperability key from
[`signing/vectors-v1.json`](../../signing/vectors-v1.json). Verify each
`manifest` against the file's `public_key_b64`; nothing needs composing and no
surrounding state needs inventing.

## Checking it

```bash
python3 scripts/check-skills-package-vectors
```

It runs as a pre-commit hook and as an explicit step in `validate.yml` —
explicit as well as hooked, because the workflow's hygiene step carries a `SKIP`
list and a corpus gate should not be one edit away from silently not running.

Parses the required-case list out of
[`schema-descriptor/v1.md`](../../schema-descriptor/v1.md) and maps each
requirement to the cases discharging it, then checks that no manifest carries a
defect other than the one its case names. Both halves exist because both were
got wrong by hand first: an audit compared against a transcription whose reading
had stopped a bullet short, and two vectors were refusable for a reason other
than the one under test.

## The rule this corpus exists to enforce

**All three implementations consume it unchanged** — the runtime that loads
packages, the SDK that authors them, and the Hub that admits them.

That is the whole point. The defect this estate has today is a package the SDK
writes, the Hub admits, and the device refuses: three implementations of one
format, disagreeing. A corpus each repo interprets for itself would reproduce
that; a corpus each repo loads verbatim cannot.

**Producer and verifier MUST NOT share code.** These files are hand-authored
beside the contract rather than generated from any implementation.

## What is not here

**No Tier D parameter, and no vector exercising one.** v3 removes Tier D from
package authority rather than describing it, so there is nothing to schematise.
The refusals are here — a package declaring `action_tier: D`, a Tier D action,
or `bypass_llm` — because prohibiting something is not the same as leaving the
prohibition untested. A rule with no vector is one an implementation can omit
while claiming conformance.

**An infinity as a value.** `Infinity` is not JSON. The one case needing it
carries its declaration as `schema_text` instead, so a strict reader is not
taken down by a single case.
