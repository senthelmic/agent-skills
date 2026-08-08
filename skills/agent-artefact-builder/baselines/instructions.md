# Baseline: project instructions

What a good instruction file looks like. Mechanics are in
[../generators/instructions.md](../generators/instructions.md); this file is
opinion, and a team may replace it — see [README.md](README.md).

---

## This file is different from every other artefact

**It is loaded on every session, whether or not it is relevant.** Skills and
sub-agents are routed to; this file is always there.

That changes the economics completely. A wasted line in a skill costs tokens
when the skill triggers. A wasted line here costs tokens forever, in every
session, for every developer, for the life of the repository.

Be ruthless. **Aim for 40 to 60 lines.** A repository whose instruction file has
grown past 100 lines has almost always accumulated material that belongs in a
skill.

---

## The test for every line

**Would an agent get this wrong without being told?**

If it would guess correctly, delete the line.

| Delete | Keep |
|---|---|
| "This is a TypeScript project." | "This is a pnpm workspace; run every command from the root with `--filter`." |
| "Tests are in the tests folder." | "Integration tests need `docker compose up db` running first." |
| "We use Express." | "Every route is registered in `src/routes/index.ts`; a route file alone does nothing." |

The left column is visible from the file tree in three seconds. The right column
is not visible at all until someone writes it down.

---

## Commands, written literally

`pnpm test --filter web`, never "run the test script for the web package".

An agent that has to guess at a command will guess, and a wrong command
generally fails loudly the first time and quietly thereafter.

**Include the precondition, not just the command.** A command that fails without
a running database or an env file is not usable knowledge until the requirement
is written next to it. This is one of the most common real gaps.

Prefer the command the CI workflow runs. It is the one that is known to work,
because it runs on every push.

---

## "Where the code lives" is a map, not an inventory

**Name the directories an agent would look for and not find.** Skip the ones it
would find immediately.

Every entry answers "what decision does this directory hold?", not "what is in
this directory".

| Weak | Strong |
|---|---|
| "`src/` — source code" | "`src/api/` — one file per route; the shape of the response is set here, not in the service layer" |
| "`tests/` — tests" | "`tests/e2e/` — runs against a real database; slow, so it is not in the default `test` script" |

In a monorepo, list the packages with their paths and what each one is for. The
package list is the single highest-value thing in the file, because an agent
that picks the wrong package writes correct code in the wrong place.

---

## What does not belong here

| Belongs in | Not here |
|---|---|
| A skill | Anything about one library's usage |
| The conventions section | Style rules, naming, error shapes |
| The code-review artefact | What a reviewer checks |
| The repository README | Why the project exists, who the customers are, the roadmap |

An instruction file that answers "what is this and how do I run it" is doing its
job. An instruction file that tries to answer everything is a file nobody
maintains.

---

## Keep the self-test line

The `FLOOR-OK-<repo-slug>` line at the bottom lets a developer confirm in ten
seconds that the file is actually being loaded, in their own editor, with no
platform-team involvement.

It is one line, and it is the only proof available that any of this is working.
Do not remove it to save space.

---

## What a reviewer would send back

- A command that is described rather than written.
- A command with an unstated precondition.
- A directory listing that repeats what `ls` shows.
- Library usage rules that belong in a skill.
- Anything an agent would have assumed anyway.
