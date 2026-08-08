# The report

**Path: `.agent-floor/report.md`.** Stable. The team chooses whether to commit
it; if they do, their git history becomes the only record of how the repository
changed. This skill keeps no history of its own and cannot backfill any.

---

## Framing

**"What your agents cannot do here yet."** Not a checklist. Not a gap report.

**No score, no level, no percentage, no `3/6`, no grade.** A score invites teams
to compare themselves against each other, and comparison turns an invitation
into a ranking.

Every gap is written as a **capability sentence**, not a missing-file sentence:

> Claude Code cannot run your tests without asking, because no build or test
> command is written down anywhere it reads.

not

> MISSING: build command

Status values are `absent`, `present`, `unverifiable`. **Never `fail`.**

---

## The exact format

````markdown
```yaml
date: <YYYY-MM-DD>
floor_version: 1.0.0
target: both | claude | copilot
capabilities:
  build_test_run: absent | present | unverifiable
  project_and_layout: absent | present | unverifiable
  coding_standards: absent | present | unverifiable
  skills: absent | present | unverifiable
  saved_prompts: absent | present | unverifiable
  sub_agents: absent | present | unverifiable
stubs_unfilled: <n>
```

## What your agents cannot do here yet

<One capability sentence per absent capability, ordered by what fails first:
build/test/run, then project and layout, then coding standards, then skills,
then saved prompts, then sub-agents.

Each sentence names the agent, what it cannot do, and why. Findings that rest
on criterion 3 (specific) are marked "(judgement call)".

If a capability is `unverifiable`, say what stopped the check.>

## What exists but we cannot yet help you build

<The split between what is missing and what this skill can currently help with.
Never let a later phase read as a broken promise: if something is out of scope
for phase 1, say so here plainly rather than leaving the team to discover it.>

## Copilot surface reach

<Computed from reference/reach-matrix.md. Write "Copilot Chat in Visual Studio
sees nothing here", not "a file is missing". Omit this section entirely if the
target is `claude`.>

## Stubs written

<Each stub path, one per line, with the sentence it carries. Then: "<n> of <n>
are still unfilled." Say plainly that a stub counts as absent and is not
progress.

Then one line pointing at `.agent-floor/backlog.md` for everything else worth
building, and saying that filling a stub frees a slot so the next survey writes
the next ones. Do not repeat the backlog here — one list, one place.>

## What this report does not tell you

- This is advisory. It is not a certification and it gates nothing.
- It confirms that build and test commands are written down. It does not
  confirm that they work; nothing here was executed.
- It judges gaps against what this repository declares about itself. A
  repository whose own manifests are wrong is judged on what they say.
````

The three limits in the last section are printed **verbatim**, in the body of
the report, not buried at the end of a long appendix.

---

## Why the YAML header is in the same file as the prose

A split data file and human file would drift apart, and the later measurement
phase needs the header at a stable path with git history behind it.

The tension between a machine-readable block and a helping tone is real. It is
mitigated by placement and wording — the header is short and sits above prose
written for a person — not dissolved. Do not try to make the header friendly,
and do not move it out.

---

## A repository already above the floor

Three lines. Nothing else.

```markdown
Claude Code and GitHub Copilot both have what they need in this repository.
Checked on <date> against floor version 1.0.0: all six capabilities present.
Nothing to do.
```

No congratulations, no score, no offer of further help, no upsell.

The backlog is still written. Being above the floor does not mean there is
nothing left worth building, and the backlog is a menu rather than a demand.
