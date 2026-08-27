---
name: ori-rigorous-review
description: Review Ori code, contracts, documentation, issues, and technical research with evidence-first, safety-aware rigor. Use for merge decisions, design reviews, defect verification, or research that informs the Ori roadmap.
---

# Ori Rigorous Review

Applies across the whole Ori estate — runtime, specs, gateway, CLI, edge
firmware, canonical JSON, phone and web surfaces — not to any one repository.
Where this document names a command, it is an example of the class of gate to
run, not the only one; find the equivalent in the repository in front of you.

## Purpose

Produce a decision the maintainer can act on: merge, do not merge, valid issue,
partial issue, follow-up, or a bounded research conclusion. Do not mistake a
plausible narrative, a green suite, or a passing mock for proof that an Ori
claim holds in its supported operating path.

Use this skill for review and investigation. Do not change, commit, push, or
post externally unless the current request explicitly authorizes that action.

## The question that governs the review

For every important claim, ask: **what would have to be false for this to be
theatre?** Then test that join.

Common joins: configuration parsed but not used; state recorded but not visible
or durable; a field carried but not verified; a vector vendored but never
consumed; a contract saying more than the implementation can enforce; a success
result returned before dispatch or persistence occurs; a check that passes only
because an earlier check already refused the input.

A claim survives when the join is exercised, not when nothing contradicts it.

## Does this reach physical authority?

Ori acts on the world, so one boundary sits above the rest: **the reviewed
runtime release owns autonomous safety action, and nothing else confers it** —
not a skill, a model, an entitlement, a cloud service, or a remote command.

Apply that test before deciding a change is ordinary. It is not answered by the
title of the PR, because a change that reaches physical authority rarely
announces itself: a configuration field becomes a trip point when a
release-owned constant multiplies it, a commercial entitlement becomes a safety
gate when protection depends on it, and a serial-port setting becomes a
measurement error when the wrong value is silently discarded.

Read `references/physical-safety-review.md` when the change touches, or could
reach, any of:

- actuation, relays, coils, contactors, or commissioning;
- action tiers, approval workflows, or safe defaults;
- safety thresholds, rated capacities, or the constants applied to them;
- firmware execution, interlocks, or orphan-mode behaviour;
- entitlement, licensing, or connectivity where protection could depend on it;
- any claim, in code or documentation, about what a deployment protects.

Skip it for work that cannot reach those — hub APIs, dashboards, docs tooling,
pricing or market research, developer ergonomics.

## First establish the review boundary

Read the repository's own guidance and the relevant issue, PR, contract, and
current diff before reaching a conclusion. Where a repository carries a
`PRINCIPLES.md`, it governs structural change; where it carries a `CLAUDE.md`
or `AGENTS.md`, that records decisions already settled. A review that reopens
either needs an argument, not a preference.

For a code change, trace every acceptance criterion through production code,
configuration, tests, operational documents, and release/install behavior. For
an issue, compare the claim with existing issues, implementation, tests, and
history before calling it new, duplicate, or partial.

Review the exact revision under discussion. Treat repository state as current
truth; use prior decisions only to identify what must be rechecked. Preserve
unrelated local changes. If a PR or merged change needs a clean inspection,
use an isolated worktree rather than altering the maintainer's branch.

## Evidence-first review

Start with one holistic pass, not serial comments. Cover the material surfaces
that apply:

- correctness and failure handling;
- authority, safety, security, and privacy boundaries;
- specifications, wire compatibility, canonical bytes, and versioning;
- configuration from declaration through its actual consumer;
- tests, generated fixtures, migrations, documentation, CI, release, and
  deployment behavior;
- sibling repositories, vector vendoring, and staged merge dependencies.

Run focused checks first, then the relevant full suite. Identify the applicable
test, static-analysis, and policy/provenance gates for the repository in front
of you — the commands differ, and not every repository has all three:

| Class | Where to look |
| --- | --- |
| Tests at the claimed scope | the language's runner, plus any hardware, container, or release-only path the change touches |
| Lint, format, types | the repo's `pre-commit` config, linter, formatter, and type checker, run on every changed file |
| Policy and provenance | capability or matrix guards, vendored-vector drift checks, schema or contract validators, licence and supply-chain guards |

A repository that lacks a class is not thereby exempt: a contract repo with no
type checker still needs its schemas validated, and a repo with no pre-commit
config still needs its formatter run. Where a gate is missing that the change
would have benefited from, that absence is itself worth reporting.

Run the commands the change claims to have passed, at the claimed scope. Green
CI is evidence, never a substitute for inspecting what was actually exercised.
Check the version, platform, and toolchain matrix wherever a defect could be
specific to one: source that imports on one interpreter and fails on the next,
a script that runs on macOS and breaks on Linux, or a check that only executes
on a tag will all pass a single local run.

Prefer behavioral assertions over textual or structural assertions. A test
should observe the real side effect or refusal reason, not only an attribute,
mock call, function name, or document phrase. When mutation testing is useful,
mutate the exact behavior claimed and confirm the named test fails for that
property, rather than incidentally through a crash or unrelated lookup.

### Do not ratify a tool's blindness

When a change makes an analysis tool report less than before, the fix is the
tool's visibility, never a test updated to expect the smaller answer. A refactor
that moves a read behind a helper can make an extractor report that nothing
reads the setting; asserting that output as expected makes the suite green by
institutionalising a falsehood, and anything derived from it inherits the gap.

An assertion whose expected value is empty, absent, zero, or "unknown" needs
positive proof that it should be. Treat one as a finding until it has that.

### The tool that produces the evidence is also under review

A corpus checked only by the tool that wrote it certifies that tool's defects.
Keep a generator and an independent verifier that share no code — and, where
the artifact crosses a language boundary, in different languages, since two
implementations of one runtime are reproduction without independence. Confirm
each negative case is negative **for its declared reason**: a reject vector
that verification accepts is worse than a missing one, because it reports
coverage that does not exist.

Read a fixture set against the contract's own stated rules, not only against
the verifier. A normative example can violate a rule that no test checks,
because the rule belongs to another layer; it will still be read as an
illustration of that rule. A fixture nothing can catch has to be right by
inspection.

Reproduction means a reader can reproduce it. Check that documented commands
work from a clean checkout by someone else: no absolute local paths, no pin to
a commit that predates the tool it names, no dependency on the author's
environment or installed toolchain.

## Reviewing your own work

Self-review before handoff is **required, and never sufficient**. It raises the
floor; it does not clear a change for merge.

Reviewing your own work is a different job from reviewing someone else's,
because the thing most likely to be wrong is an assumption you cannot see. You
already believe the design is sound — that belief is what produced the code, and
re-reading with it intact finds typos rather than defects. So do not re-read.
Go looking specifically for **what you would have wanted to be true**:

- The check you decided was unnecessary because an earlier one covers it.
- The test whose name describes the property but whose body asserts something
  weaker — an attribute, a mock call, a string.
- The case you left out because constructing it was awkward.
- The number you quoted from memory rather than measuring.
- The claim you made in a summary that no command in your history proves.

Then verify the claim, not the construction. It is easy to confirm that the code
does what you wrote it to do, and that is not the question — the question is
whether it does what you *said* it does. Re-run the exact commands at the exact
scope claimed, and where a boundary is load-bearing, mutate it and confirm the
named test fails for that property.

**Always state what remains unverified.** A handoff that lists only successes
misrepresents its own coverage. Name what was simulated, deferred, tested on the
host only, or dependent on another repository or on hardware.

Self-review does not substitute for independent review of shared contracts,
Tier D or physical-authority changes, release and install work, or any claim of
HIL proof. Those need a second reviewer regardless of how clean the first pass
looked.

## Contracts, vectors, and cross-repository work

Treat shared contracts as the authority for shared semantics. Write or amend a
contract before merging an implementation that invents transport grammar,
authenticator purpose, trust anchors, artifact identity, or rejection meaning.
Keep an implementation PR explicitly blocked and reconcile it against the
merged contract before merge when work must proceed in parallel.

For canonical-byte or signed artifacts, verify exact-byte reproduction,
signer/key-purpose separation, rejection ordering, and the cases that
differentiate selection from trial verification. Do not manufacture test state
or an invented schema merely to make a vector pass. A vector corpus is useful
only when its intended consumer actually loads it; otherwise account for the
gap explicitly with an owner, proof status, and tracking item.

When a specs merge changes vendored vectors, perform a deliberate re-vendor in
each consuming repository after the merge, pin the squash-merge commit where
appropriate, and rerun drift and reproduction checks. A pin naming a commit
that is not on `main` looks authoritative and points at nothing. Do not
silently refresh a consumer while the contract is still pending. After any
merged PR, sync the affected local `main` in every repository it touches before
building follow-on work.

Distinguish representable from enforced. A field that a contract defines, a
corpus exercises, and no production consumer reads has closed a
representability gap and nothing else. Say which one the change achieved, and
name the repository where enforcement will actually live.

## Documentation and research

Documentation must describe actual code and its proof level, not an intended
future design. Use precise proof states: proposed, implemented, host-tested,
target-built, HIL-proven, manufactured/pilot-observed, and
production/commercially validated. State the highest level genuinely achieved
and the remaining boundary.

For research that affects product, safety, policy, pricing, platforms, or
roadmaps, verify current facts with primary or official sources. Separate
direct evidence from inference, cite each material claim near its source, and
do not treat a vendor sandbox, a demo, or a benchmark as production proof.

## Reporting

Lead with the decision and proof level. Classify every finding:

| Severity | Merge effect |
| --- | --- |
| **Blocker** | Do not merge. A safety, authority, correctness, or contract boundary fails. |
| **Not mergeable as written** | Directionally right; the evidence does not yet support the claim. |
| **Qualification** | Merge is defensible once the limitation is stated in the PR or issue. |
| **Follow-up** | Real, out of scope, needs its own item with an owner and repository. |

For each verified finding, state:

1. severity and merge effect;
2. exact source, contract, test, or operational evidence;
3. real impact, including the boundary that fails;
4. the smallest correct fix and how it must be validated.

Consolidate findings so a collaborator can address them in one pass.

Reporting a defect on someone else's work is an outward action and carries a
higher bar than a private conclusion: read the issue, comment, or instruction
the author was following before calling their change a regression. Work removed
on purpose looks identical to work lost by accident. Check for an existing issue
or PR before filing either. If the user asks for feedback in their voice, post
only after the holistic pass, then read back the stored comment.

A greenlight must say what was revalidated and what remains unproven, deferred,
or dependent on another repository or HIL.
