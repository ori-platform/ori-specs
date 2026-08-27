# Agent Skills

Canonical, tool-neutral source for the agent skills used across the Ori estate.

A skill is instructions an AI coding agent loads when it recognises the task.
These live here rather than in any one developer's home directory because they
carry review and safety practice that applies to every Ori repository, and
practice that exists only on one laptop cannot be reviewed, versioned, or
corrected.

Nothing under `agent-skills/` is tool-specific. Installers copy it into whatever
layout a given tool expects.

## Install

```bash
scripts/install-agent-skills --codex     # into ~/.codex/skills
scripts/install-agent-skills --claude    # into ~/.claude/skills
scripts/install-agent-skills --check     # report drift, install nothing
```

Both tools can be installed at once (`--codex --claude`). For a project-scoped
install rather than a personal one, pass a destination:

```bash
scripts/install-agent-skills --claude --dest /path/to/repo/.claude/skills
```

Re-run after pulling `main`. `--check` exits non-zero when anything is missing,
stale, or left over from an older version, so it works in a shell profile or CI
step.

> **Verify on your own machine before relying on it.** These target paths are
> the documented defaults for each tool, but they have been exercised here only
> on macOS with a sandboxed `HOME`. Run `--check` after your first install and
> confirm the agent actually loads the skill before treating either path as
> settled for the team.

## Why copies, not symlinks

A symlink is invisible to the tool that reads it and follows a branch checkout,
so a teammate on a feature branch would silently be running that branch's
version of the review rules. A copy is inert until reinstalled, and `--check`
reports when it has fallen behind.

`MANIFEST.json` pins a SHA-256 for every shipped file, and the installer
verifies the source against it in both directions before copying anything: a
file that changed, a file that was added, a file that was *deleted*, and a skill
directory that no longer exists all stop the install. The deletion case is the
one worth naming — without it, every surviving file still matches its digest, so
an install succeeds and quietly prunes the deleted file from every recipient.

**The manifest is a drift detector, not an authorization boundary.** It records
what the source said, not who approved it: anyone can edit a file, run
`--manifest`, and install from an uncommitted tree. The installer says so when
`agent-skills/` is dirty, and that is all it can do. Authorization happens in
the pull request, which is the reason the canonical copy lives in a repository
rather than in a home directory.

## Changing a skill

Edit under `agent-skills/`, run `scripts/install-agent-skills --manifest`,
review the digest diff, and open a PR. Bump the skill's `version` in
`MANIFEST.json` when behaviour changes.

A change to a skill is a change to how every agent reviews Ori code. Review it
as you would a change to a contract, not as documentation.

A directory is a skill only if it contains a `SKILL.md`. Anything else under
`agent-skills/` is refused rather than ignored, because a scratch directory that
gets manifested is then installed into every teammate's tool as though it were
reviewed guidance.

## Retiring a skill

Delete its directory and run `--manifest`. The skill is not simply dropped: it
is recorded under `retired`, and installers then **remove it** from machines
that already carry it, while `--check` reports one that is still present.

That registry is the whole mechanism. Without it the manifest merely forgets the
name — there is nothing left to compare against, so `--check` reports success
while the tool goes on loading guidance the project has withdrawn.

Fill in the retirement reason the generator leaves as a `TODO`; it is the only
record of why the guidance changed.

Retirement applies only to names the manifest has held. A skill directory in
your tool that this repository never shipped belongs to whoever put it there,
and the installer does not touch it.

## Skills

| Skill | Version | What it is for |
| --- | --- | --- |
| `ori-rigorous-review` | 1.0.0 | Evidence-first, safety-aware review for merge decisions, design reviews, defect verification, and roadmap research. Detailed physical-authority rules load on demand from `references/`. |
