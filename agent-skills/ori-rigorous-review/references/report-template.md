# Review Report Template

The shape a review report takes, whichever tool wrote it. A repository may
copy it into its own pull request template on its maintainer's word. Nothing
checks it; the maintainer reading the pull request does, and a block that is
present and hollow is the incident that would buy a checker.

Every field below is a fact about what was done, never a judgement about the
change. Whether a change is irreversible is not asked here: nothing in a pull
request can contradict an author's answer, and a field nothing can contradict
becomes the answer.

```markdown
## Review

Reviewer: <adversarial pass | second reviewer | third-party model | self-review>
Revision: <commit reviewed> against <base>
Skill: ori-rigorous-review <version, from the installed manifest>

### Findings

1. <severity> — <one line>
   Class: <the class this is an instance of>
   Sweep: <surfaces searched for the class, and what was found | delegated to
     <who>, class named | not run, because <reason>>
   Evidence: <reproduced: command or procedure, observed result, oracle |
     reasoned: exact source or texts, and what execution would settle>
   Guard: <the fail-closed guard on the product, and the test that fails
     when the property is broken | local fix only, because <reason>, residual
     owned by <who>>

(A review with no findings still states the sweep it ran and the classes it
searched for. "Sweep: none" is allowed and needs its reason; it is written so
it can be questioned, which an absence cannot.)

### Not verified

- <what was simulated, deferred, host-only, unobservable, or dependent on
  another repository or on hardware> — <issue | dated write-off with the
  accepted risk | named dependency and the check that closes it>

("Not verified: none" needs its reason as much as "Sweep: none" does. A
handoff that lists only successes misrepresents its coverage, and an empty
list is the most common way to do that.)

### Environment

<what this reviewer's environment could not run: sandbox, missing toolchain,
absent device, unreachable provider, no source for the other repository>

### Decision

<merge | do not merge | merge with the qualifications above | no verdict:
this reviewer cannot merge the change; findings and unobservables stand>
Oracle reached: <executed at the real entry point | prior code read for
equivalence | bench or handset | third party | none beyond reading>
Stopping rule: <met: sweeps done, guards revalidated, one further pass found
no new class | not met: <which condition>>
```

Notes a repository's copy should keep:

- **Bot pull requests carry no block**, and the exemption holds only while
  every commit on the pull request is the bot's. A human push onto a
  dependency bump makes it a human change, and dependency bumps reach the
  trust surface without looking like they do.
- **A release chore is human and near-mechanical.** "Sweep: none, release
  metadata only" is the correct and expected content, so nobody invents a
  finding to fill the field.
- **The oracle line is the one that decays first.** It names a fact: what
  was executed, read, or touched. Boilerplate there is a hollow block.
