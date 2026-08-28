# `safety-profile/v1` vectors

Six files, split by what a consumer does with them.

| File | Carries | A consumer |
| --- | --- | --- |
| `profiles.json` | The v1 profile set | Vendors it byte-for-byte, pins its digest, and populates its safety registry from nothing else |
| `profile-load.json` | Profile sets in, `loaded` or `malformed_profile` out | Proves the closed grammar its loader applies to the set it ships |
| `legacy-actions.json` | The three actuator-specific names the runtime registers, their outcomes, and where each may be commanded from | Proves its outcome resolver maps every legacy name and never lets `close_protected_circuit` be a profile outcome |
| `activation.json` | Accepted zones in, activated profiles and refusals out | Proves activation, its order, and that installed skills are not an input |
| `evaluation.json` | Profile, zone facts, one reading in; verdict out | Proves the evaluators and the credibility rules |
| `lifecycle.json` | A sequence of events in; trip state after each out | Proves arm, trip, latch, restart, reset, and that nothing external moves the state |

`profiles.json` is normative data, not an example. Every profile in it is a
`candidate`, which a consumer must neither activate nor execute. The
conformance files therefore each carry a `fixtures` list of ratified profiles
that exist only there; cases name profiles by id, resolved against the file's
fixtures first and the shipped set second. A case that would evaluate or arm a
candidate fails the checker.

## Reading a case

Every case has a `name`, unique within its file, and a `note` saying what it
demonstrates.

**Profile load.** `profiles` is a complete set, loaded under the closed grammar
in file order. A refused case carries `must_name`: identifiers the refusal must
contain. Each malformed case carries exactly one defect.

**Legacy actions.** One case per legacy name: `outcome`,
`permitted_as_profile_outcome`, and `permitted_from` drawn from `profile_trip`,
`tier_c_approved`, `local_arm`, `local_reset`.

**Activation.** `zones` have already passed all twelve stages of the
commissioned binding contract; only the fields activation reads are carried.
`installed_skills` is present so that a consumer can prove it changes nothing.
`profile_set` is `fixtures` or `shipped`. `expect.activated`,
`expect.refused` and `expect.pending_ratification` are in zone order, then
profile order; `expect.startup` is `start`, `start_degraded` (development posture with
a refusal) or `refuse` (hardened posture with a refusal).

**Evaluation.** `reading.value` is a JSON number, a JSON string (tests
`non_numeric`), a JSON boolean (tests `boolean`), or `{"non_finite": "nan" |
"+inf" | "-inf"}`, which the consumer converts to the IEEE value before
evaluating — JSON cannot carry those values directly. `trip_point` is the
literal the consumer must compute for that profile on that zone. A rejected
case carries exactly the defect it names; the two cases that prove the reason
order carry two and declare `"tests_order": true`.

**Lifecycle.** The first event is always `startup` with the `durable_state`
found on disk: `none`, `armed` or `tripped`. Each event names `expect_state`
afterwards; an event that executes an outcome names `expect_outcome`, a refused
event names `expect_refusal`, and a rejected reading names `expect_rejected`.
Readings are credible unless the event carries a `quality` or a non-finite
value.

## Checking it

```bash
python3 scripts/check-safety-profile-vectors
```

It runs as a pre-commit hook and as an explicit step in `validate.yml`. The
corpus is hand-authored; the checker recomputes every expectation from the
contract's rules and refuses the corpus on any disagreement, so a literal
cannot contradict the rule it illustrates. It also checks that every verdict
and refusal the contract names has a case, and that a refused activation case
is refusable for exactly the reason it names.

The checker is not a second implementation. The runtime's evaluator is the
first; ratification waits for a verifier in another language that shares code
with neither.
