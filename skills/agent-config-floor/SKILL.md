---
name: agent-config-floor
description: Survey a repository and report what Claude Code and GitHub Copilot cannot do in it yet, writing stubs and a ranked backlog of everything worth building. Use when a repo has little or no agent configuration, when someone asks what config an agent needs here, or when they ask to audit or set up agent config. To then build one of those artefacts, use agent-artefact-builder instead.
---

# The agent config floor

This skill helps a team get a repository to the point where Claude Code and
GitHub Copilot can actually work in it.

**Floor version: 1.0.0 (phase 1, Bootstrap).**

## What this skill is, in one paragraph

The **floor** is the minimum agent configuration every repository should have.
It is a minimum, never a template. This skill does the **survey run**: cheap and
bounded, it reads a fixed set of files, reports what the agents cannot do here
yet, writes a one-line placeholder file (a **stub**) at the correct path for
every artefact the repo should have, and lists everything else worth building.

Filling one of those in is a separate skill, run separately, whenever the team
likes. Nothing forces them past the survey run.

## Three outputs, three jobs

| Output | Job | Capped? |
|---|---|---|
| The stubs | Put the shape of a configured repository into the file tree, where a pull request will show it | Yes — five, beyond the floor artefacts |
| `.agent-floor/report.md` | Say what the agents cannot do here yet | — |
| `.agent-floor/backlog.md` | The exhaustive ranked list of everything worth building here, of every type, with evidence | **No** |

**The stub cap bounds files on disk. It never bounds what the team is told.** A
file tree full of placeholders is clutter; a short list of recommendations when
we know more is a different and worse problem. The backlog exists so neither
happens.

## Filling an artefact

A separate skill: [agent-artefact-builder](../agent-artefact-builder/SKILL.md).
It fills one artefact per run, reads the backlog and the report, and takes the
team's own baselines and the developer's own instructions as input.

Point the developer at it. **Do not fill an artefact from this skill.**

> Fetch `<published URL of agent-artefact-builder/SKILL.md>` and follow it.

The two skills sit side by side in the same directory, over a URL and in a local
clone alike. When resolving a `../agent-artefact-builder/…` link, **resolve the
`..` yourself before fetching — never send a URL containing `..`.**

## How a team gets this

One line, pasted into Claude Code or GitHub Copilot:

> Fetch `<published URL of this SKILL.md>` and follow it.

The agent retrieves the skill and runs it. With no network, the fallback is
`git clone --depth 1 <repo>` and pointing the agent at the local path.

The only prerequisite is an agent — which is the thing this skill exists to help
the team adopt. There is no CLI, no npm package, no Node dependency, no server,
and no telemetry.

**Reading the rest of this skill.** Every link below is relative. Resolve each
one against the location you loaded *this* file from, whether that was a URL or
a local path. If you fetched this over the network, fetch the linked file from
the same directory. Do not fetch a file until you actually need it — the survey
run needs only `floor.md`, `survey-run.md`, `reference/plain-english.md` and the
files those point to.

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
8. **Write in plain English.** Everything this run says to the developer, and
   every piece of prose it writes into a file, must be readable by someone whose
   first language is not English. Short sentences, no idioms, no metaphors, and
   every technical term explained the first time it is used. This never means
   fewer facts or a softer finding. Follow
   [reference/plain-english.md](reference/plain-english.md), whichever model is
   running this.

## Step 1 — ask the one survey question

The survey run asks exactly one question, because it cannot be read from the
code:

> Which agents is this for — Claude Code, GitHub Copilot, or both?
> (Default: both.)

Record the answer as `target: claude | copilot | both`. It decides which file
is authored and which is generated from it. See
[reference/paths.md](reference/paths.md).

If the developer does not answer, use `both`.

## Step 2 — do the survey run

Follow [survey-run.md](survey-run.md), start to finish.

**If a report already exists and the developer wants to fill an artefact**,
this is not the skill for that. Point them at
[agent-artefact-builder](../agent-artefact-builder/SKILL.md) and stop.

**If the developer has not said which they want:** do the survey run, then offer
the unfilled stubs and the top backlog rows so they can pick one — and hand off.
The whole design depends on runs being small and separate.

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
| [reference/paths.md](reference/paths.md) | Which path each artefact goes to, per target. |
| [reference/reach-matrix.md](reference/reach-matrix.md) | Which Copilot surfaces read which file. Versioned data this toolkit owns. |
| [reference/report-template.md](reference/report-template.md) | The exact shape of `.agent-floor/report.md`. |
| [reference/backlog-template.md](reference/backlog-template.md) | The exact shape of `.agent-floor/backlog.md`, and the merge rules. |
| [reference/stubs.md](reference/stubs.md) | The stub format and the stub set. |
| [reference/copilot-translation.md](reference/copilot-translation.md) | Claude-native to Copilot conversion, and what is lost. |
| [reference/init-commands.md](reference/init-commands.md) | Each agent's own setup command, what it writes, and how to check whether it has been run here. |
| [reference/plain-english.md](reference/plain-english.md) | How both skills write. Read once per run, before writing anything. |

`floor.md`, `paths.md`, `copilot-translation.md`, `backlog-template.md`,
`init-commands.md` and `plain-english.md` are also read by the builder skill.
They are shared, not copied — copies drift.

## What this skill deliberately does not do

- It does not gate, block, or enforce anything. There is no CI check.
- It does not measure token spend or response quality. That is a later phase.
- It does not carry a library of pre-written skills. Every artefact is
  generated for this repository from this repository's own code.
- It does not standardise how teams work. Anything a team adds above the floor
  is theirs, and this skill does not touch it.
