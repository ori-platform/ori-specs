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
