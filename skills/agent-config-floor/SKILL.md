---
name: agent-config-floor
description: Find out what Claude Code and GitHub Copilot cannot do in this repo yet, and help the team fill the gaps one artefact at a time. Use when a repo has little or no agent configuration, when someone asks what config an agent needs here, or when they ask to set up CLAUDE.md, copilot-instructions, skills, saved prompts, or sub-agents.
---

# The agent config floor

This skill helps a team get a repository to the point where Claude Code and
GitHub Copilot can actually work in it.

**Floor version: 1.0.0 (phase 1, Bootstrap).**

## What this skill is, in one paragraph

The **floor** is the minimum agent configuration every repository should have.
It is a minimum, never a template. This skill has two kinds of run. The
**survey run** is cheap and bounded: it reads a fixed set of files, reports what
the agents cannot do here yet, and writes a one-line placeholder file (a
**stub**) at the correct path for every artefact the repo should have. An
**artefact run** is opt-in and fills exactly one of those stubs with real
content.

The team works through the stubs at whatever pace suits them. Nothing forces
them past the survey run.

## How a team gets this

One line, pasted into Claude Code or GitHub Copilot:

> Fetch `<published URL of this SKILL.md>` and follow it.

The agent retrieves the skill and runs it. With no network, the fallback is
`git clone --depth 1 <repo>` and pointing the agent at the local path.

The only prerequisite is an agent — which is the thing this skill exists to help
the team adopt. There is no CLI, no npm package, no Node dependency, no server,
and no telemetry.

**Always latest, never pinned.** The floor's version travels with the skill, so
a team learns the floor moved by running it, never by being told.

## Rules that hold for every run

1. **This is help, not an audit.** Where a judgement could go either way,
   choose the reading that gets a struggling team running sooner.
2. **Never phone home.** Everything this skill produces is a local file. The
   team decides whether to commit it and whether to share it.
3. **Leave nothing of your own behind.** The only files you write are files the
   *team* owns: their config, their stubs, their report. Do not install this
   skill into the repo. Do not add a tool directory.
4. **The report never counts toward the floor.** Neither does any stub.
5. **Stay inside the reading budget for the run you are doing.** When you
   cannot answer a question inside the budget, write `could not determine` and
   move on. Do not explore further.
6. **Never produce a score.** No level, no percentage, no `3/6`, no grade. A
   score invites teams to compare themselves against each other, which turns an
   invitation into a ranking.
7. **Never assert what you did not verify.** Any claim you cannot check against
   the code is written into the artefact as a `TODO(floor):` question for the
   developer, not as a statement of fact.

## Step 1 — ask the one survey question

The survey run asks exactly one question, because it cannot be read from the
code:

> Which agents is this for — Claude Code, GitHub Copilot, or both?
> (Default: both.)

Record the answer as `target: claude | copilot | both`. It decides which file
is authored and which is generated from it. See
[reference/paths.md](reference/paths.md).

If the developer does not answer, use `both`.

## Step 2 — choose the run

**If this is the first time in this repo, or `.agent-floor/report.md` does not
exist:** do the survey run. Follow [survey-run.md](survey-run.md).

**If a report already exists and the developer wants to fill an artefact:** do
an artefact run for that one artefact. Follow
[artefact-run.md](artefact-run.md).

**If the developer has not said which:** do the survey run first and then offer
the list of unfilled stubs, so they can pick one. Do not start an artefact run
without the developer choosing the artefact.

Never do more than one artefact run without being asked. The whole design
depends on runs being small and separate.

## The six capabilities

All six are required. They are ordered by what fails first for a developer
using an agent in an unconfigured repo.

| # | Capability | Portable between the two agents? |
|---|---|---|
| 1 | How to build, test and run | Content is portable, the file format is not |
| 2 | What the project is and where the code lives | Content is portable, the file format is not |
| 3 | Coding standards and conventions | Content is portable, the file format is not |
| 4 | Skills | Yes — one `SKILL.md` serves both agents |
| 5 | Saved prompts | No — must be authored once per agent |
| 6 | Sub-agents / custom agents | No — must be authored once per agent |

**One artefact is mandatory by name in every repository, whatever the
technology stack: a code-review skill or saved prompt.** It is mandatory to
*have*. What it says is the team's own business.

## Files in this skill

| File | What it is for |
|---|---|
| [floor.md](floor.md) | The rubric. Read it when checking a repo *and* when generating for one, so the two cannot drift apart. |
| [survey-run.md](survey-run.md) | The survey run, start to finish. |
| [artefact-run.md](artefact-run.md) | The artefact run, start to finish. |
| [reference/paths.md](reference/paths.md) | Which path each artefact goes to, per target. |
| [reference/reach-matrix.md](reference/reach-matrix.md) | Which Copilot surfaces read which file. Versioned data this toolkit owns. |
| [reference/report-template.md](reference/report-template.md) | The exact shape of `.agent-floor/report.md`. |
| [reference/stubs.md](reference/stubs.md) | The stub format and the stub set. |
| [reference/copilot-translation.md](reference/copilot-translation.md) | Claude-native to Copilot conversion, and what is lost. |
| [generators/](generators/) | One generator per artefact. Never one generator that writes everything. |

## What this skill deliberately does not do

- It does not gate, block, or enforce anything. There is no CI check.
- It does not measure token spend or response quality. That is a later phase.
- It does not carry a library of pre-written skills. Every artefact is
  generated for this repository from this repository's own code.
- It does not standardise how teams work. Anything a team adds above the floor
  is theirs, and this skill does not touch it.
