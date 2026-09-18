# Briefing The Reviewer And Handing Off

Load this when you wrote the change and are about to brief its independent
review, or hand it to a maintainer. The reviewer's method is in
`adversarial-review.md`; this file is the other half of the arrangement, and
it is where the record shows the discipline most often lapses.

## What the reviewer receives

The reviewer is a separate agent that starts without your context. Give it:

- the exact revision, the issue, the governing contracts, and the prior
  state (`HEAD`, or the merged base) so it can read old against new;
- the entry points a caller actually reaches, and how to run them;
- **every claim the change makes, as a list of things to falsify**: "this
  refactor preserves the authentication behaviour of three routes"; "the
  count identity holds for every snapshot"; "no test can pass with the guard
  removed";
- **your own suspicions, as hypotheses**: "the device supplies `sent_at_ms`
  and I never bounded it"; "look hardest at the executor thread against
  `onDestroy`". The reviews that found the most were the ones pointed at a
  named doubt. The clock-poisoning blocker was found because the brief said
  to check it, and the reviewer then drove a poisoned snapshot through the
  route rather than reading the store.

Withhold your justification. "It is safe because check X runs before check
Y" is the sentence that makes the reviewer read the diff the way you wrote it,
and a second model given your framing reasons carefully inside it. Reasoned
findings made from inside an author's framing have been confidently and
specifically wrong. The reviewer forms its own model first; your reasoning
can follow when it asks.

If the change deviates from a contract, send the deviation bare, without the
argument for it, and let the reviewer judge it cold from the other side of the
boundary. A deviation that survives belongs in the contract as an amendment,
not as one implementation's private liberty; otherwise the next implementer
writes the version the contract still describes.

A draft contract gets a second kind of review that no reader can give: each
implementer runs the draft against the code they own and reports where it
forbids something they do, permits something they refuse, or says two things.
Textual review is necessary and finds a different class. One amendment went
through two textual rounds, which found a missing replacement rule, an
ambiguity, a self-contradicting example, and two missing obligations, all
real and all about the text; the five defects about the world, including a
sentence that would have regressed staleness detection for every phone that
buffers offline, were found only by the receiver's implementer running the
paragraph against the receiver. Send a draft to every implementer, not only
to a reader.

Ask for the report in the shape the main skill defines: decision first, each
finding with its class, its sweep, and whether it was reproduced or reasoned.

Give a reviewer that mutates its own worktree, or stop touching yours until
it reports. A mutation pass copies a file aside, breaks it, runs a test and
restores it, and if you keep working in the same tree while it runs, two
things happen and neither announces itself. Your own test runs land
mid-mutation and fail intermittently with a different test each time, which
reads exactly like a real race; a dozen runs were spent one night hunting a
flake that did not exist. And your edits land under the reviewer, so its
findings about anything you touched are worth nothing, and it cannot tell.
The tell is a failing test that moves between runs while the code is
unchanged.

## What you do with a finding

Decide, for every valid finding, whether its class can recur on another
field, route, caller, platform, or release path. When it can, the fix is a
guard that fails closed, and the local repair is incomplete without it. The
record is clear on which compounds: the repository that had already written,
in prose, that a reading's age is the greater of the device's interval and the
receiver's own, made the same
mistake on its next route, because prose is not checkable. A test that
enumerates every producer-supplied timestamp on the surface, requires each to
be classified as a producer fact or excluded from receiver ordering, and
refuses to start on one it does not recognise would have made the blocker
unwritable, at no cost on any later change.

The guard must fail for the property it names. Mutate the property and watch
the named test fail for that reason, not through a crash or an earlier refusal.
A guard has matched its own comment; a guard has scanned only JSON while the
seed it was built to refuse sat in a Python literal; a guard claimed to read
every tracked file and skipped four extensions. Each passed its tests.

No gate may exist only on a release path. Before any tag, merge-only step, or
hosted-runner check is trusted, document the ordinary invocation and test that
it runs, on an input carrying the same hazardous property as the real one,
asserted two-sided. Three release tags were spent learning this, and the third
was spent by a check whose ordinary run used a probe term that could not
exercise the defect the real document carried.

## What leaves the PR body

A merged PR body is where unverified claims go to be forgotten. At the last
count, one product repository carried around twenty open items that existed
nowhere else: handset cases never exercised, concurrency proven in one
process, a database path with no production runtime. Before handoff, every
item in the "what is not verified" list gets exactly one of:

- an issue naming the repository, the owner, the proof needed, and what it
  blocks;
- a dated write-off stating why the case cannot arise and what risk is
  accepted;
- a named dependency in another repository, with the check that closes it.

Say which. The list itself stays in the PR body; the disposition is what
makes it survive the merge.

## Shape before merge, never a pin

Communicate a pending artifact's shape freely: the fields, the rejection
reasons, the invariants a consumer must hold. Never communicate its identity.
A fixture regenerated by a review's fixes has a different digest from the one
the implementation first produced, and a squash merge rewrites the source
commit even when every vendored byte is identical; a consumer that pinned
either before merge holds a pin that looks authoritative and points at
nothing. A version tag is spent the moment it is pushed; published test keys
sat in trust-anchor positions for real time. None of these is recoverable in
the sense that matters: you cannot unpublish.

Make the rule enforceable where you can. A consumer's vendored manifest must
name a forty-hex-character source commit, so a placeholder pin fails the suite
until the upstream merge exists. A pin that names no commit is then
unwritable, and nobody has to remember the discipline.

## What depth the change needs

Irreversibility sets the depth, and it runs in two directions: toward the
customer, where a payments defect takes money or quietly ends protection
without saying so, and toward the trust surface, where a tag, a digest, a
published key, or a vendored fixture is spent the moment another party pins
it. Neither needs a customer present: a version tag is spent permanently when
pushed, and published test keys once sat in trust-anchor positions with no
customer anywhere near.

Two tiers, and the floor is the same for both. Every change gets the
self-review, the gates, the adversarial pass with its class sweep, and the
labelled report. A change that is irreversible in either direction adds the
rest of the stopping rule's further holistic pass, a second independent
reviewer, the third-party model, and the bench or the strongest external
oracle that can be reached before release. A change irreversible in neither
may stop when the pass and its sweep come back clean and the report says what
was not run. Nothing here removes the pass; what irreversibility adds is the
independent oracles, because those are what a hurried week drops first, and
a discipline that is dropped under pressure protects nothing.

The third-party model is a reviewer, and owes what every reviewer owes: an
oracle and a labelled finding. Execute for "does it work"; read the prior
code for "is it the same as what worked"; say which was done. A model that
reads a diff and approves it adds a signature, not evidence, and the estate's
record holds a confident, specific wrong claim from exactly that shape. Brief
it as you would brief any reviewer, and ask it what it ran and what broke.

A greenlight is a statement about the boundary that was covered. It is never
a statement that the change is safe in production, on a handset, or in
another repository unless those oracles ran, and the handoff says which did.
