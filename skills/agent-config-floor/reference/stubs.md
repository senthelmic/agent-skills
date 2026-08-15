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

The `<what this artefact is for>` half is prose a person reads, so it follows
[plain-english.md](plain-english.md): one short sentence, no jargon, no
metaphor. The fixed wording after it is fixed — do not reword it, because the
`TODO(floor):` marker and the sentence around it are what the next survey run
matches on.

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

Both keys are needed. Copilot requires `name` **and** `description` on a
`SKILL.md`, and `name` must be lowercase with hyphens — verified against
GitHub's skills documentation on 2026-08-14. A Copilot custom agent requires
only `description`, but writing both costs nothing and keeps one template.

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
and `.github/agents/<name>.agent.md` instead. See [paths.md](paths.md).

### Recommended artefacts — capped at five stub files

Recommendations come from step 2 of the survey run — skills from the stack,
saved prompts from repeated operations, sub-agents from repository shape. They
are the only unbounded set, so the stub files are capped at **five**, taken in
rank order across all three types together.

**The cap bounds files on disk. It does not bound the recommendation.**
Everything above the cap is written into
[backlog-template.md](backlog-template.md)'s file, `.agent-floor/backlog.md`,
with its evidence and its proposed path. The cap exists because a file tree full
of placeholders is clutter — not because a team should be told less than we
know.

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
slot. When the developer has filled two stubs and re-runs the survey, the run
may write up to two more, taking the next ones by rank from the backlog. This is
how a repository with many recommendations works through them over time instead
of being permanently limited to the first five.

---

## Stubs, the report and the backlog

Three outputs, three jobs. Keep them separate.

| File | Job |
|---|---|
| The stubs | Put the shape of a configured repository in the file tree, where a pull request will show it. Capped. |
| `.agent-floor/report.md` | Say what the agents cannot do here yet. States how many stubs are unfilled. |
| `.agent-floor/backlog.md` | The exhaustive list of everything worth building, ranked, with evidence. Not capped. |

**The report states how many stubs remain unfilled** and points at the backlog
for the rest. It does not repeat the list. A repository stalled at eight empty
stubs is told so plainly, and is not counted as progress. Stubs are not
achievements, and neither is a long backlog.
