# Stubs

The survey run writes a small placeholder file at the correct path for every
artefact the repository should have and does not.

**Why a file and not a list in the report.** A report is easy to ignore; a
directory of files is not. A beginner sees the shape of a configured repository
in their file tree, and an unfilled stub is visible in every pull request.

---

## The format

A stub is frontmatter, where the path requires it, plus **exactly one line** of
body:

```markdown
TODO(floor): <what this artefact is for, in one sentence>. Unfilled — this file
contains no guidance. Run the floor skill and pick this artefact to fill it.
```

No headings, no template body, no example sections.

### Frontmatter, where the path requires it

A `SKILL.md` or a sub-agent file with no frontmatter is malformed, and the agent
will either refuse to load it or list it as a broken entry. So stubs at those
paths carry the minimum valid frontmatter, and **the description tells the agent
not to use it**:

```markdown
---
name: <kebab-case-name>
description: Unfilled floor stub. Ignore this — it contains no guidance.
---

TODO(floor): ...
```

For a saved prompt at `.claude/commands/<name>.md`, use the same `description`
line without `name`.

For `CLAUDE.md` and `.github/copilot-instructions.md`, no frontmatter — the one
body line alone.

---

## The two rules, both mandatory

### 1. `TODO(floor):` means absent

The rubric never counts a marked stub as present, under any circumstances.
Without this rule, this skill writes stubs and its own next run reports the
repository as having skills — a false pass manufactured by our own hands.

Adding frontmatter does not weaken this. A stub is well-formed *and* absent, and
those are different criteria. See [../floor.md](../floor.md).

This holds for real artefacts too: a genuine `CLAUDE.md` with one `TODO(floor):`
line still in it counts as `absent` until that line is resolved.

### 2. A stub tells the agent to ignore it

An unfilled stub must cost a reading agent nothing beyond its one line and its
frontmatter. That is why there is no template body, and why the `description` is
a routing signal that says *do not use this*. Stubs are the single exception to
"this skill leaves nothing behind", and being cheap to read is the price of the
exception.

---

## What gets a stub

Write a stub only where the capability is `absent`. Never overwrite a file that
has real content. If a file already carries a `TODO(floor):` marker, leave it
exactly as it is.

### Floor artefacts — never capped

These are bounded by the floor itself, so there is no clutter risk and hiding
any of them would hide part of the floor.

| Capability | Stub path (target `both` or `claude`) | Sentence |
|---|---|---|
| 1, 2, 3 | `CLAUDE.md` | what this project is, where its code lives, how to build, test and run it, and the conventions it follows |
| 1, 2, 3 | `.github/copilot-instructions.md` (target includes Copilot) | the same guidance as `CLAUDE.md`, at the only path every Copilot surface reads |
| 4 | `.claude/skills/code-review/SKILL.md` | how code is reviewed in this repository — the mandatory artefact, whatever the stack |
| 5 | `.claude/commands/<name>.md` | a saved prompt for a task this team repeats |
| 6 | `.claude/agents/<name>.md` | a sub-agent for a task worth handing to a separate agent |

For target `copilot`, use `.github/skills/`, `.github/prompts/<name>.prompt.md`
and `.github/agents/<name>.md` instead. See [paths.md](paths.md).

### Stack skills — capped at five

Recommended skills come from stack detection and are the only unbounded set. Cap
them at **five**. Rank by which skill would most change what an agent writes in
this repository, and **name the ones you did not stub in the report**, with
their evidence, so nothing is silently dropped.

**Name the stubs from what you detected**, not from a generic list. If the
manifests show React and Prisma, the stubs are `.claude/skills/react/SKILL.md`
and `.claude/skills/prisma/SKILL.md`, each sentence carrying its evidence. If
nothing was detected, write only the floor artefacts above.

---

## How the cap counts

**A non-portable artefact and its translation are one artefact, not two.** A
saved prompt at `.claude/commands/deploy.md` and its Copilot copy at
`.github/prompts/deploy.prompt.md` are the same piece of work in two files. Both
are written; they count once.

**The cap counts only stubs that are still unfilled.** Filling a stub frees a
slot. When the developer has filled two stack-skill stubs and re-runs the
survey, the run may write up to two more, taking the next ones by rank. This is
how a repository with many detected libraries works through them over time
instead of being permanently limited to the first five.

---

## Stubs and the report

**The report states how many stubs remain unfilled**, and names every
recommended skill that did not get one. A repository stalled at eight empty
stubs is told so plainly, and is not counted as progress. Stubs are not
achievements.
