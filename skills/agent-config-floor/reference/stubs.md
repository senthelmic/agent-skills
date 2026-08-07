# Stubs

The survey run writes a one-line placeholder file at the correct path for every
artefact the repository should have and does not.

**Why a file and not a list in the report.** A report is easy to ignore; a
directory of files is not. A beginner sees the shape of a configured repository
in their file tree, and an unfilled stub is visible in every pull request.

---

## The format

Exactly this, and nothing more:

```markdown
TODO(floor): <what this artefact is for, in one sentence>. Unfilled — this file
contains no guidance. Run the floor skill and pick this artefact to fill it.
```

One line of content. No headings, no frontmatter, no template body, no example
sections.

---

## The two rules, both mandatory

### 1. `TODO(floor):` means absent

The rubric never counts a marked stub as present, under any circumstances.
Without this rule, this skill writes eight stubs and its own next run reports
the repository as having skills — a false pass manufactured by our own hands.

This holds for real artefacts too: a genuine `CLAUDE.md` with one `TODO(floor):`
line still in it counts as `absent` until that line is resolved.

### 2. A stub tells the agent to ignore it

An unfilled stub must cost a reading agent nothing beyond that one line. That is
why there is no template body. Stubs are the single exception to "this skill
leaves nothing behind", and being cheap to read is the price of the exception.

---

## The stub set

Write a stub only where the capability is `absent`. Never overwrite a file with
real content. If a file already carries a `TODO(floor):` marker, leave it as it
is.

| Capability | Stub path (target `both` or `claude`) | Sentence |
|---|---|---|
| 1, 2, 3 | `CLAUDE.md` | what this project is, where its code lives, how to build, test and run it, and the conventions it follows |
| 1, 2, 3 | `.github/copilot-instructions.md` (target includes Copilot) | the same guidance as `CLAUDE.md`, at the only path every Copilot surface reads |
| 4 | `.claude/skills/code-review/SKILL.md` | how code is reviewed in this repository — the mandatory artefact, whatever the stack |
| 4 | `.claude/skills/<recommended>/SKILL.md` | one per recommended skill from stack detection |
| 5 | `.claude/commands/<name>.md` | a saved prompt for a task this team repeats |
| 6 | `.claude/agents/<name>.md` | a sub-agent for a task worth handing to a separate agent |

For target `copilot`, use `.github/skills/`, `.github/prompts/<name>.prompt.md`
and `.github/agents/<name>.md` instead. See [paths.md](paths.md).

**Name the stubs from what you detected**, not from a generic list. If the
manifests show React and Prisma, the stubs are
`.claude/skills/react/SKILL.md` and `.claude/skills/prisma/SKILL.md`, each
sentence carrying its evidence. If nothing was detected, write only the
mandatory code-review stub plus the instruction files.

Do not write more than eight stubs. Beyond that the file tree stops reading as
structure and starts reading as clutter, and nobody fills any of them.

---

## Stubs and the report

**The report states how many stubs remain unfilled.** A repository stalled at
eight empty stubs is told so plainly, and is not counted as progress. Stubs are
not achievements.
