# Handoff: agent config floor, phase 1

**Date:** 2026-08-07
**Repo:** `senthelmic/agent-skills` (public)
**Branch:** `main` (everything merged and pushed)

This file sits beside the spec it continues, at
`.scratch/agent-config-floor/handoff.md`. Paths below are relative to the repo
root.

---

## Where the authoritative context lives

Do not re-derive any of this. Read it.

| What | Where |
|---|---|
| The spec being implemented | `.scratch/agent-config-floor/spec.md` |
| The decisions behind it, and the ubiquitous language | `.scratch/agent-config-floor/map.md` |
| The 14 resolved decision tickets | `.scratch/agent-config-floor/issues/` |
| The implementation | `skills/agent-config-floor/` (16 files) |
| How a team runs it | `README.md` at the repo root |
| Repo conventions (issue tracker, triage labels, domain docs) | `CLAUDE.md`, `docs/agents/` |

**There is no tasks file.** The user referred to one in the first message; it
does not exist. The work was derived section by section from `spec.md`.

---

## State: phase 1 is built, merged, and in its first trial

Four commits, all on `main`:

- `d76efed` — the 16-file skill
- `9908209` — relative-link resolution for URL loading
- `26e8f49` — the README
- `0299749` / `001b6c8` — three defects found in the first trial

Commit messages carry the reasoning; read them rather than asking.

**The published install line** (this is what the user runs on other machines):

> Fetch `https://raw.githubusercontent.com/senthelmic/agent-skills/main/skills/agent-config-floor/SKILL.md` and follow it.

Raw GitHub URLs cache for about five minutes after a push. Verify against a
commit-SHA URL if a change seems missing; `?v=<timestamp>` does **not** reliably
bust that cache.

---

## Decisions made during implementation that the spec did not settle

Each of these is mine, not the spec's. Say so if the user questions one.

1. Skills live at `skills/<name>/` in this repo.
2. Floor version `1.0.0`; the Copilot reach matrix is versioned separately
   (`1.0.0`, dated 2026-08-07) with a staleness warning in the file.
3. Coding standards write into the `## Conventions` section of the instruction
   file, not a separate file — a convention an agent does not load does not
   exist.
4. The stub cap (see below), twice revised.

---

## The first trial, and what it found

The user ran the skill on a second machine against a real TypeScript repo
(TWX — Express/knex/mssql/inversify/ag-grid/bullmq/react-query). It worked.
Evidence was a photo of the terminal, not a saved artefact.

Three defects were found and **are already fixed** in `0299749`:

1. The eight-stub cap capped the wrong set. Floor artefacts are bounded by the
   floor itself, so capping them can hide part of the floor. Now: floor
   artefacts uncapped, stack skills capped at five and ranked, and every skill
   that did not get a stub is named in the report with its evidence.
2. A non-portable artefact and its Copilot translation were counted as two
   stubs. They are one artefact in two files; they now count once.
3. Stub `SKILL.md` files had no frontmatter, so Claude Code registered them as
   broken skills. They now carry minimal valid frontmatter whose `description`
   tells the agent to ignore them. They still count as absent, because
   well-formed and absent are separate rubric criteria.

---

## What the next session should pick up

### 1. Finish the trial (this is the priority)

`spec.md` §11 lists what the first trial exists to answer. Two are still open,
and both were deliberately left to be corrected by real use rather than by
design argument:

- **Did the report read as help, or as an audit?** The framing is the whole bet.
- **Did the stubs read as useful structure or as clutter, and did anyone
  actually fill one?**

Also unanswered: was six questions per artefact run too many or too few; did
ambiguity detection find real disagreements or invent them; did anything push
the agent past the reading budget.

**Ask the user for the generated `.agent-floor/report.md` from the test repo.**
Nothing in this repo records it. Judging the report's tone from a photo is not
good enough.

### 2. Re-run the survey on the test repo

The four skill stubs there were written under the old format and still lack
frontmatter. Deleting them and re-running is the cleanest check that the three
fixes work. `bullmq` and `@tanstack/react-query` should now receive stubs.

### 3. Verify the reach matrix before anyone else uses this

`skills/agent-config-floor/reference/reach-matrix.md` was transcribed from
`map.md` constraint 4, **not** verified against GitHub's live documentation. It
carries a `Last verified` date and a staleness warning. When GitHub changes that
matrix, every past verdict silently becomes wrong. This is flagged in `map.md`
under "Not yet specified" as unowned.

### 4. Do not reopen these

- **npm packaging.** Raised by the user and talked through in detail. Rejected:
  npm has not reliably run uninstall lifecycle scripts since npm 7;
  `--ignore-scripts` breaks `postinstall`; copied-in files would count toward
  the floor and manufacture a false pass (ticket 05); a version number is
  pinning, which §2 rules out on purpose; and it reintroduces the Node
  dependency ticket 14 closed as moot. If the user asks again, this is their
  call — but record the reasoning against ticket 05 so it is not undone twice.
- **Phase 2 and 3 work.** Stack-aware recommendation sharpening, proper Copilot
  translation, and all measurement/telemetry are explicitly later phases.

---

## How to work on this

- **Plain English.** The user is not a native English speaker. Define jargon,
  avoid idioms and metaphors. This is a saved memory, not a guess.
- **The spec is the record of *what*; the tickets are the record of *why*.**
  Where they disagree, the ticket explains the reason and the spec governs the
  build.
- The user pushes back on arbitrary numbers and unstated reasoning, and is right
  to. Both stub-cap revisions came from that. Say plainly when a choice is yours
  rather than the spec's.
- Commits use `--no-ff` merges from a short-lived branch, and end with the
  `Co-Authored-By: Claude Opus 5` trailer.
- `.DS_Store` is untracked at the repo root and should stay that way.

---

## Suggested skills

| Skill | When |
|---|---|
| `mattpocock-skills:grilling` | Before changing any floor rule. The stub cap survived two revisions only because it was challenged. `map.md` names this as a default skill for this effort. |
| `mattpocock-skills:domain-modeling` | If the ubiquitous language in `map.md` needs a new term, or a decision deserves an ADR. Also named as a default for this effort. |
| `mattpocock-skills:research` | For the two open research tickets — Copilot's prompt and custom-agent formats (ticket 12) and whether Copilot CLI generates instructions (ticket 13). Also the right tool for verifying the reach matrix against GitHub's live docs. |
| `mattpocock-skills:code-review` | Before the next merge to `main`, reviewing against `spec.md` as the originating document. |

`/prototype` is listed in `map.md` for "what should this look like" questions.
It is unlikely to be needed now that phase 1 is built.

---

## Nothing sensitive

No keys, tokens or credentials appear in this work. The repo is public by
design; the skill never sends anything anywhere.
