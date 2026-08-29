# `runtime-config/v2` vectors

Three files.

| File | Carries | A consumer |
| --- | --- | --- |
| `schema.json` | The provisioning document's declaration: every path, type, bound, default, the delegations, and the retired table | Vendors it byte-for-byte, pins its digest, and validates every provisioning document against it |
| `schema-load.json` | Descriptor fragments in, `loaded` or `schema_error` out | Proves it refuses a bad declaration at schema load, before any document exists |
| `documents.json` | Documents in, `accepted`, `legacy_document`, or `refused:<verdict>` out | Proves its loader closes the surface, types without coercion, and refuses retired paths with the right verdict |

`schema.json` is normative data. It adopts `schema-descriptor/v1` and adds
`description`, `pattern`, `exclusive_minimum`, `entries`, `delegate` and the
schema-level `retired` table; the contract defines each.

A schema-load fragment may carry `{"non_finite": "nan" | "+inf" | "-inf"}`
where a bound is expected, which the consumer converts to the IEEE value
before loading, because JSON cannot carry those values directly.

## Reading a case

Every case has a `name`, unique within the file, a `note`, a `document`, and
`expect`. A refused case also carries `must_name`: full paths and words the
refusal must contain, so a consumer proves it names the boundary rather than
the leaf. Each refused case carries exactly one defect, and the checker refuses
the corpus if a second one is present — a document refusable for a reason
other than the one it names reports coverage that does not exist.

Delegated subtrees follow the contract's rule for a consumer without the
delegate: sensor protocol keys pass through (`sensor-configuration/v1` is
implemented wherever v2 is), and `skills[].config` keys other than
`approval_timeout_seconds` are refused naming `skills-package/v3`.

## Checking it

```bash
python3 scripts/check-runtime-config-vectors
python3 scripts/check-runtime-config-vectors --write-appendix
python3 scripts/check-runtime-config-vectors --runtime-examples <ori-runtime checkout>
```

The first form runs as a pre-commit hook and as an explicit step in
`validate.yml`. It loads the schema under every load-time rule of the
descriptor core and the extensions, classifies every case, checks isolation
and coverage, and checks that the field appendix in `v2.md` is what the schema
generates. The second regenerates that appendix. The third applies the
contract's migration rules to the runtime's shipped example files and reports
what a v2 consumer would refuse; it is evidence for a change, not a gate, since
the examples live in another repository. It needs PyYAML
(`python3 -m pip install pyyaml`), which the gated forms do not.

The checker is a reader of the corpus against the contract, not a second
implementation. The runtime's loader is the first implementation, and an
independent verifier in another language is what ratification waits for.
