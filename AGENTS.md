# AGENTS.md — ori-specs Guide for AI Coding Agents

This file is for any AI coding agent working on this repository: Claude Code,
Cursor, Codex, Gemini, Copilot, or any other tool.

## What this repository is

`ori-specs` holds the contracts that bind the Ori estate together — wire
formats, canonical byte rules, authority separation, rejection semantics, and
the golden vector corpora that prove an implementation conforms.

It is not documentation of what the code does. It is the authority for what the
code must do. Implementations in `ori-runtime`, `ori-gateway`, `ori-cli` and
`ori-edge-firmware` vendor these vectors and are expected to fail their own CI
when they diverge.

## What that means for a change here

**A contract change is not a documentation change.** Amending a document in this
repository changes what every consuming repository must accept and refuse, and
several of those consumers govern physical actuation. Review it as a change to
behaviour.

**Write the contract before the implementation.** An implementation that invents
transport grammar, authenticator purpose, trust anchors, artifact identity, or
rejection meaning has made a decision this repository owns. Where work must
proceed in parallel, keep the implementation PR explicitly blocked and reconcile
it against the merged contract before merge.

**A merge here breaks consuming repositories, including ones you did not
touch.** Their drift checks compare a pinned `source_commit` against this
repository's `main`, and a squash merge rewrites that commit even when every
vendored byte is identical. After any merge, re-vendor and re-pin in each
consumer before pushing anything from it.

**Vectors are the proof, and the tool that produces them is under review too.**
Keep a generator and an independent verifier that share no code, and confirm
each reject case is refused for its declared reason. A reject vector that
verification accepts reports coverage that does not exist. Read a fixture set
against the contract's own rules as well: an example can violate a rule no test
checks, because the rule belongs to another layer, and it will still be read as
an illustration of that rule.

**Say what is representable and what is enforced.** A field this repository
defines, a corpus exercises, and no production consumer reads has closed a
representability gap and nothing else. Name the repository where enforcement
will actually live.

## Agent skills

`agent-skills/` is the canonical, tool-neutral source for the review skills used
across the estate, installed with `scripts/install-agent-skills`. Its README
explains the layout, the manifest, and how to change or retire a skill.

A change to a skill is a change to how every agent reviews Ori code. Review it
as you would a change to a contract.

## Review before handoff

Self-review before handing off work is **required, and is never sufficient
proof.** State plainly what remains unverified: what was simulated, deferred,
tested on the host only, or left dependent on another repository or on hardware.
A handoff that lists only successes misrepresents its own coverage.

It does not replace independent review for shared contracts, Tier D or
physical-authority changes, release and install work, or any claim of HIL proof.

The method is the `ori-rigorous-review` skill in `agent-skills/`. Read it before
reviewing; do not reimplement it here.
