# Generator: the code-review artefact

**Mandatory in every repository, by name, whatever the technology stack.** It is
mandatory to *have*. What it says is entirely the team's own.

It may be a skill or a saved prompt — either satisfies the floor. Default to a
skill, because a skill serves both agents from one file.

**Writes to** `.claude/skills/code-review/SKILL.md`, or
`.claude/commands/code-review.md` if the team prefers a saved prompt.

**Read [../baselines/code-review.md](../baselines/code-review.md) first.** This file is the
mechanics; the baseline is what a good one looks like, and the team may have
replaced it. See
[../reference/baseline-resolution.md](../reference/baseline-resolution.md).

---

## Why this one is mandatory

Every other artefact recommendation depends on the stack. This one does not,
because every team reviews code and every team's review standards are invisible
to an agent until someone writes them down. A COBOL team and a React team get
the same treatment here.

**Do not standardise the content.** How a team reviews code is house style. This
generator captures what *this* team already does; it does not import a review
checklist from anywhere.

---

## Step 1 — read the diff surface

Recent commits and the files they touch. That is the scope — not the whole
repository.

Look at, roughly, the last 20–30 commits:

- What kinds of change are common here?
- Do commit messages or pull request descriptions follow a shape?
- Are there review comments visible in the history that show what this team
  cares about?
- What breaks often, judging by fix commits?

---

## Step 2 — ask, at most six

Good questions here, because the code rarely answers them:

- What must never be merged without a test?
- Is there a part of this codebase that needs extra care in review (auth,
  billing, migrations, anything generated)?
- What does the team keep having to say in reviews?
- Are there changes that need a specific person or team to look at them?

**"I don't know" is expected here more than anywhere else**, because the person
running this may be a new joiner and this is the most tribal of the six
capabilities. Every "I don't know" becomes a `TODO(floor):` line naming the
question, so someone who does know can answer it later in a pull request.

Never leave this artefact unwritten because the answers are missing. A file of
open questions at the right path is the point — it is visible, and someone will
fill it.

---

## Step 3 — write

```markdown
---
name: code-review
description: Use when reviewing a change in this repository, or when asked to
  check work before opening a pull request.
---

# Code review

## What this team checks

- <specific check>
- <specific check>

## Areas that need extra care

- `<path>` — <why>

## Before opening a pull request

- <command to run — from the instructions file, so it stays consistent>

TODO(floor): <each open question, named>
```

Rules:

- **The checks must be this team's**, taken from history or from the interview.
  Not a generic review checklist.
- **Commands come from the instruction file**, so the two cannot disagree.
- The `description` is the routing signal: write when to use it.

---

## Step 4 — re-check

Every path and command: one lookup each. Anything unverified becomes a
`TODO(floor):` question.

Then confirm capability 4 in the report. Remember: while any `TODO(floor):` line
remains in this file, the capability is still `absent` — see
[../../agent-config-floor/floor.md](../../agent-config-floor/floor.md), criterion 1. Say that to the developer plainly, so
the remaining status does not look like the run failed.
