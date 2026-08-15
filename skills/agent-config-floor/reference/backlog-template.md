# The backlog

**Path: `.agent-floor/backlog.md`.** Stable. Written by the survey run, beside
the report.

**The backlog is exhaustive. The stub cap is not a limit on it.** The cap bounds
how many placeholder files land in the file tree; it never bounds what the team
is told. Every artefact worth building appears here, of every type — skills,
saved prompts and sub-agents alike.

Like the report, the backlog **never counts toward the floor**, and the team
chooses whether to commit it.

---

## Framing

**"Everything worth building here, in the order we would build it."** A menu,
not a progress bar.

The rules from [report-template.md](report-template.md) hold here too:

- **No score, no percentage, no `3 of 12 done`.** A count of finished rows reads
  as a target, and a target turns an invitation into an obligation.
- **Every row carries its evidence.** "`package.json` lists `bullmq`", never
  "you use BullMQ". The developer confirms or dismisses; you did not measure
  real usage, because that needs reading the budget forbids.
- **Never assert what you did not verify.** A row you are unsure about is
  written with what you actually saw, not with what you assume it means.
- **Plain English**, following [plain-english.md](plain-english.md). This covers
  the prose around the table and the `Evidence` cell in each row. Column
  headings, the `Type` values and the `Status` values are fixed and are not
  rewritten. Simple wording never means fewer rows.

---

## The exact format

````markdown
```yaml
date: <YYYY-MM-DD>
floor_version: 1.0.0
target: both | claude | copilot
```

# Everything worth building here

Written by the agent config floor on <date>. This file is advisory. It gates
nothing, scores nothing, and counts toward nothing. Add your own rows; a later
survey will leave them alone.

Ranked by which artefact would most change what an agent writes in this
repository.

| # | Type | Name | Proposed path | Evidence | Stub | Status |
|---|---|---|---|---|---|---|
| 1 | skill | code-review | `.claude/skills/code-review/SKILL.md` | mandatory in every repository, whatever the stack | yes | unfilled |
| 2 | skill | bullmq | `.claude/skills/bullmq/SKILL.md` | `package.json` lists `bullmq` | yes | unfilled |
| 3 | sub-agent | migration-reviewer | `.claude/agents/migration-reviewer.md` | 41 files under `db/migrations/` | no — over the stub cap | unfilled |

## How to use this list

Filling an artefact is a separate run. Pick a row and say so.

Rows marked "no — over the stub cap" have no placeholder file yet. Filling a
stub frees a slot, so re-running the survey writes the next ones by rank.
Nothing on this list is dropped.

## What is not on this list

<Anything the run considered and rejected, with the reason — one line each.
A library that is present but too thin to deserve a skill belongs here, not
silently omitted. If nothing was rejected, leave this section out.>
````

---

## The columns

| Column | Rule |
|---|---|
| `#` | Rank. 1 is the artefact that would most change what an agent writes here. |
| `Type` | Exactly one of `skill`, `saved-prompt`, `sub-agent`. Instructions and coding standards are floor artefacts with fixed paths and do not belong on a ranked list. |
| `Name` | kebab-case. This becomes the directory or file name. |
| `Proposed path` | The path from [paths.md](paths.md), for the recorded target. |
| `Evidence` | What you actually read, quoted. Never an inference stated as fact. |
| `Stub` | `yes`, or `no — over the stub cap`, or `no — <reason>`. |
| `Status` | `unfilled`, or `filled <YYYY-MM-DD>`, or `dismissed by the team`. |

**A non-portable artefact and its Copilot translation are one row, not two.** A
saved prompt at `.claude/commands/deploy.md` and its copy at
`.github/prompts/deploy.prompt.md` are the same piece of work. Name the authored
path in the row; the translation is implied by the target.

---

## Re-running merges, never replaces

A second survey of the same repository **must not throw away the first
backlog.** If `.agent-floor/backlog.md` exists, read it first and merge:

1. **A row's identity is its proposed path.** Match on that, not on rank.
2. **Keep every existing row.** Including rows the team added by hand, and rows
   whose evidence you can no longer see.
3. **Refresh `Stub` and `Status`** from what is now on disk. A row whose path
   now holds a real artefact becomes `filled <today>`. A row whose path holds a
   `TODO(floor):` stub stays `unfilled` — see [stubs.md](stubs.md), rule 1.
4. **Append what is new**, then re-rank the whole list.
5. **Never delete a row.** A row the team no longer wants is theirs to mark
   `dismissed by the team`, and a dismissed row is never re-proposed.

If there is no existing backlog, write a fresh one.

---

## A repository with nothing to recommend

Write the header and one line:

```markdown
Nothing beyond the floor is recommended here. The manifests, CI workflows and
directory layout did not show anything specific enough to be worth an artefact
of its own.
```

Do not pad the list to look useful. A short honest backlog is the correct output
for a small repository.
