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

The parties that keep producing findings:

- **Clocks the code does not own.** A device clock, a payment provider's
  timestamp, a handset with no battery-backed time, a Pi with no RTC. The
  same defect has been found five times in two repositories: a reading dated
  by a fast device clock that never went stale; a reply window that refused on
  one millisecond of provider-ahead skew; a payment ordered by receipt time
  when the provider's time was the fact; an adoption test that compared local
  receipt against a provider's creation time; and a status snapshot pinned
  forever by a device sending the largest representable timestamp, while the
  receiver answered `accepted`. The rule depends on which fact the timestamp
  represents, and the wrong generalisation has already been made once: taking
  the lesser of the producer's and the receiver's time still lets a clock that
  has moved backward freeze an honest later state. **Which state the receiver
  currently holds is the receiver's fact, and its ordering never depends on
  producer wall time. When a reading or a payment happened is the producer's
  fact, and it is kept as the producer reported it, but never compared against
  a clock it does not share.** Freshness sits between them: the age the
  receiver itself observed is a ceiling no producer timestamp may raise.
  Enumerate every producer-supplied timestamp on the surface, classify each,
  and fail closed on one the guard does not recognise.
- **Producers in another repository, read at their own source.** Do not
  infer a producer from the consumer's fixture. A telemetry fixture carried a
  subscription field the provider's own corpus says a charge never carries;
  a refund parser read `reference` where the canonical shape carries
  `refund_reference`, so the stored column was blank for every real refund; a
  generated fixture held 23 shapes against a 24-shape corpus, and its own
  `>= 23` guard institutionalised the omission. Read the producer at its
  `main`, and the provider at its published corpus.
- **Other callers of shared code.** A guard placed on one loader left the
  CLI's own seed-reading path open; a shared verifier turned out to have three
  consumers, and the offline-token one let anyone with a clone forge an
  operator's approval on a physical action; an app's install action wrapped
  four distinct refusals into one "check the link" message. When a change
  hardens a helper, enumerate its callers by tooling, not by memory.
- **The operating system.** It logs the install link that carried a
  long-lived key; it reuses an inode the moment a socket is unlinked, on Linux
  and not on macOS; it lets `argv[0]` be anything, so a process-kill keyed on
  the command line kills the wrong process; it byte-compiles scripts into a
  directory the uninstaller did not expect; it reports a symlink as mode 0777.
- **Prior state and first use.** An installer replaced an unmanaged script
  the operator had written; a supervisor cleared the last exit reason on
  finding the process already alive; a release build in a stale generated
  directory packaged last week's payload; a read-only command created the
  state database, owned by whoever ran it. Ask what is on disk, in the
  database, and in the environment *before* this code runs, and whether the
  fixtures ever set that up.
- **Delivery that repeats, reorders, or arrives late.** A provider retries
  webhooks for three days; a stop delivered at 10:10 must not refuse a payment
  made at 10:05; a snapshot that fails to post is discarded, never retried, so
  two from one device cannot race, which is exactly the fact a receiver-side
  reorder window was built without knowing.
- **The environment the reviewer has and the author did not.** The
  strongest round-two finding of one release was seventeen absolute host
  paths in every payload, visible only because the reviewer's toolchain
  carried a source component the author's did not. A workflow's fixture
  trusted a hosted-runner interpreter that the runner keeps writable. An unset
  user-agent is not a neutral omission; it is the banned value, and every
  provider call the product had ever made had failed. Differences between
  your machine and the author's are an oracle. Use them, and label them, since
  the same difference also produces false blockers: two merge conditions once
  raised against a change were the reviewer's sandbox refusing sockets and a
  type checker drifting, not the change.
- **Hardware and the handset.** Nothing on this list substitutes for them.
  A test suite toggled a wired relay pin; a killed process left a coil driven;
  a driver never selected its multiplexer and every sample was a plausible
  constant; a re-read that every unit test approved overwrote a correct
  status on the phone. Say plainly when the pass could not reach them.

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
  every new column. Sweep for every other place the same recognition is done.
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
  invocation, and a test that the invocation exists.

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
