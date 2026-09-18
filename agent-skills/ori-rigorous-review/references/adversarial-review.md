# The Adversarial Pass

Load this when you are the independent reviewer of work someone else wrote.
The main skill says what to run and how to report; this file says where the
defects that matter have actually come from, so the pass goes there first.

Across the estate's review record, plain logic errors are a small minority of
what independent review finds. Almost everything else is one of two things: a
party the author never modelled, or a claim the author's own tests could not
have contradicted. Correctness inside the author's framing is the author's
job first: mutation testing shows that the properties the author conceived
are the ones the tests detect, and nothing more. This pass still reads for
correctness, but it spends its time where the author could not have looked.

## Inventory the parties before reading the diff

A diff is written from inside one process. Before adopting its framing, list
every party whose behaviour or state the change depends on, and for each ask:
what can it omit, repeat, reorder, retain, expose, reset, or supply from a
clock or a state this code does not control?

Build that inventory from the change in front of you. The list below is what
the estate has already paid for, not what exists: a reviewer who sweeps these
and stops has checked seven classes and declared the rest absent. Each entry
is a rule, with the incident that bought it kept only as proof that it was
bought.

- **Clocks the code does not own.** Three quantities, one asymmetry. *When it
  was measured* is the producer's, kept raw, and compared only against bounds
  a caller supplies, never against a clock the producer does not share. *When
  it was received* is the receiver's, and is the key for which record is
  current and which state is held; producer wall time never orders those.
  *How old it is* takes the greater of the two intervals: a producer may make
  a record older than the receiver observed, since a phone that buffered in a
  basement delivers hours-old readings a second ago, and never fresher, since
  a clock running ahead cannot make a record younger than its arrival. A
  displayed timestamp takes the lesser. Two repairs are already known wrong:
  ordering by the producer's time, which let a device pin its state with one
  large timestamp while the receiver answered `accepted`; and taking the
  lesser of the two clocks, which still lets a stopped clock hold an honest
  later state down. Five incidents in two repositories bought this rule, one
  of them on a route whose sibling carried it in prose. Enumerate every
  producer-supplied timestamp on the surface, classify each, and fail closed
  on one the guard does not recognise.
- **Producers in another repository.** Read the producer at its own `main`
  and the provider at its published corpus; never infer either from the
  consumer's fixture. A fixture carried a field the provider's corpus says the
  event never carries; another held 23 shapes against a 24-shape corpus, and
  its own `>= 23` guard institutionalised the omission.
- **Other callers of shared code.** When a change hardens a helper, enumerate
  its callers by tooling and check each against the new premise. A guard on
  one loader left a CLI's own ingress open; a shared verifier had a third
  consumer that let anyone with a clone forge an operator's approval on a
  physical action.
- **The operating system.** For every OS facility the change touches, ask
  what it logs, reuses, reports, defaults, or lets a caller supply. It logged
  the install link that carried a long-lived key; it reuses an inode the
  moment a socket is unlinked, on Linux and not on macOS; it lets `argv[0]`
  be anything, so a process-kill keyed on the command line kills the wrong
  process.
- **Prior state and first use.** Ask what is on disk, in the database, and
  in the environment before this code runs, and whether any fixture sets that
  up. An installer replaced a script the operator had written; a build in a
  stale generated directory packaged last week's payload; a read-only command
  created the state database, owned by whoever ran it.
- **Delivery that repeats, reorders, or arrives late.** Assume a message can
  be retried for days, arrive out of order, or arrive after the state it
  describes has changed, and read the producer's actual retry behaviour
  before designing against a race it cannot produce. A stop delivered at
  10:10 must not refuse a payment made at 10:05; a receiver-side reorder
  window was built against a producer that never retries.
- **The environment the reviewer has and the author did not.** Differences
  between your machine and the author's are an oracle: use them, and label
  them, because the same difference also produces false blockers. A reviewer's
  toolchain carrying one extra component exposed seventeen host paths in every
  payload; an unset user-agent was the banned value and every provider call
  the product had ever made had failed; two merge conditions once raised were
  the reviewer's own sandbox refusing sockets.
- **Hardware and the handset.** Nothing on this list substitutes for them,
  and say plainly when the pass could not reach them. A test suite toggled a
  wired relay pin; a killed process left a coil driven; a re-read that every
  unit test approved overwrote a correct status on the phone.

## Report the class and sweep it in the same round

Every finding is an instance of something. Name the class in the report, then
search every applicable surface for that class before handing back, and say
what the sweep covered. Rounds that each return one more spelling of the same
defect are the failure this rule exists for: an architecture guard needed
eleven rounds because each round named one bypass; an author wrote, in one
session, a third test satisfied by something other than the property in its
name; a fix that changed a substring check to an exact one still left the
seed scanner walking only JSON and the file scanner skipping four extensions
its docstring said it read.

Classes that recur, each stated as the sweep to run:

- **Named instance, not the rule.** A substring or regex where a parser or
  exact grammar is required: `ARM` matching `aarch64`; a comment quoting the
  API call a scaffold guard was looking for; a units allow-list that grows on
  every new column. Sweep for every other place the same recognition is done,
  and include the guard itself: a timestamp guard that recognised its domain
  by a name suffix asked about seven of the seventeen integers on its surface
  and was fail-closed over a recognition that was wide open. A guard discovers
  what it governs by type, exhaustively, and requires every member to be
  declared; a name is not a type.
- **A test satisfied by something other than its name.** Asserting
  `no_readings` on a device that never stored a reading; `key not in url`
  when the payload is base64; a release gate that `skip`s when the wheel
  fails to build; a test signed with a fresh key so removing the published-key
  guard changes nothing; a test runner argument read as a path pattern, so
  zero tests ran and the job exited non-zero unnoticed. For each new test,
  name the single-line mutation it would not catch.
- **A value checked in one place and used in another.** A signed document's
  `rated_capacity_amps: 10.0` became `0.001` through textual environment
  expansion after signature verification; an evidence harness reported a
  revision it had not run; a launcher's identity was self-asserted. Trace the
  checked value to its consumer and confirm the same bytes arrive.
- **A side effect before admission.** Packages installed before the bundle
  was authenticated; a system account created before the operator confirmed;
  rows committed before a validator raised, stranding a half-built tenant
  behind a 500. Order every write after every refusal that could still occur.
- **A boundary removed by the thing that replaced it.** Funnelling every
  handler through one barrier silently dropped the sensor-type eligibility
  check, and a current-only skill fired on a temperature reading; deleting a
  URI validator as "dead" let `file:///etc/passwd` load clean past the host
  allowlist. For a refactor, the oracle is the prior code: **what did the old
  code do that the new code does not?** Read `HEAD`'s version check by check
  against the tree. Execution cannot cheaply prove "nothing changed".
- **A probe that measures the wrong surface.** A mocked dispatcher never
  consulted the admission gate, so "the action was dispatched" said nothing
  about whether it was admitted; a 0.0 ms latency was the handler never
  running. Confirm the probe crosses the boundary it claims to measure.
- **A gate that exists only on a release path.** Three release tags were
  spent discovering that a tag-only check had never executed. Any check that
  runs only on a tag, a merge, or a hosted runner needs a documented ordinary
  invocation, and a test that the invocation exists. That is not enough on its
  own: the third tag was spent by a check whose ordinary run planted a probe
  term chosen to appear nowhere, so it fired on every pull request and never
  met the substring-versus-boundary defect the real input carried. The
  ordinary invocation must use an input with the same hazardous property as
  the real one, asserted two-sided: it fires on the hazard and stays quiet on
  the benign neighbour.

The repair is normally a surface-wide, fail-closed guard, not a local fix. The
estate already has the shape: executor registration refuses an action with no
registry entry; a liveness module refuses to import while any install status
is unclassified. A new member of a governed class must stop validation or
startup until it is classified. It must never disappear through a default, a
`.get()`, or a `skip`. Where a class-wide guard is impossible, say so, and
state the residual recurrence risk with an owner.

## Label every finding by its oracle

For each finding, state one of:

- **Reproduced.** You drove the failure through the entry point a caller
  reaches. Give the command or procedure, the observed result, and what you
  compared it against. Thirty parallel requests returning seventeen 401s and
  thirteen 500s is a finding; "the lock looks insufficient" is a hypothesis.
- **Reasoned.** The conclusion follows from source, contract, or the
  coherence of two texts, and was not executed. Give the exact evidence and
  say what execution would settle it. Some findings can only be reasoned: a
  contract example that described a network-or-serial choice while both
  adapters it illustrated construct with host, serial and port together; an
  issue whose scope item contradicted its own non-goals. Reasoned findings
  have also been wrong: a review
  named a path that did not carry the defect its class did, and advice to
  randomise a conformance seed broke two golden-vector tests.

A reasoned finding does not become reproduced because the author or CI
reports green, and it is the author's job to disprove it by execution, not by
argument. Say what your environment could not run: a sandbox, a missing
toolchain, an absent device, an unreachable provider. That is a proof boundary
the maintainer must carry forward, not background.

## When the pass is finished

A clean round is not a greenlight. A change approved on a clean round was
followed the same day by a fix that made devices refuse skills silently while
reporting healthy; supervision code approved on a clean round produced two
lifecycle issues within a fortnight; a release-candidate greenlight was
withdrawn one turn later. Each earlier round had found something new, which is
evidence that the class was still being discovered, not that it was exhausted.

Stop when all of these hold:

1. every finding's class has been swept across the surface it applies to, and
   the report says what the sweep covered;
2. each blocker's repair and its guard have been revalidated against the real
   entry point, and the guard has been mutated and seen to fail for the named
   property;
3. one further holistic pass after those sweeps finds no new class;
4. every reasoned finding and every environment limit is stated;
5. everything still open has an owner and a tracking disposition.

Two reviewers agreeing is weaker than it feels when both are instances of the
same model reading the same brief. The independent parties in any Ori review
are the prior code, the producer at its own source, a machine that is not the
author's, and the hardware. Where a change is irreversible toward a customer
or toward something another party pins, the strongest of those that can be
reached must be, before the greenlight.

The depth this rule demands is set by that irreversibility, and
`briefing-and-handoff.md` says what each tier owes. The pass, its class
sweep, and the labelled report are the floor for every change; a process that
costs hours on a copy change is abandoned the first week anyone is in a hurry,
and a discipline abandoned under pressure is worse than a cheaper one that
survives it.
