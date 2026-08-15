---
name: agent-artefact-builder
description: Build one agent config artefact properly — a skill, a saved prompt, a sub-agent, project instructions, coding standards, or a code-review artefact. Use after the agent config floor has surveyed a repository, when someone picks a stub or a backlog row to fill, or when they ask to write a skill, a slash command, a sub-agent, or CLAUDE.md for this repository.
---

# The agent artefact builder

This skill builds **one** agent config artefact at a time, for the repository it
is run in.

**Floor version: 1.0.0 (phase 1, Bootstrap).**

## Where this sits

Two skills, two jobs.

| Skill | Job |
|---|---|
| [agent-config-floor](../agent-config-floor/SKILL.md) | Survey the repository. Say what the agents cannot do here yet. Write stubs and the backlog. |
| **This one** | Fill one of those in, properly. |

Run the floor skill first. It writes `.agent-floor/report.md` (which records the
target agents) and `.agent-floor/backlog.md` (the ranked list of everything
worth building). This skill reads both.

If neither file exists, say so and point the developer at the floor skill rather
than guessing.

## How a team gets this

One line, pasted into Claude Code or GitHub Copilot:

> Fetch `<published URL of this SKILL.md>` and follow it.

**Reading the rest of this skill.** Every link below is relative. Resolve each
one against the location you loaded *this* file from, whether that was a URL or
a local path.

Some links point at the sibling skill, as `../agent-config-floor/…`. **Resolve
the `..` yourself before fetching — never send a URL containing `..`.** The two
skills sit side by side in the same directory, over a URL and in a local clone
alike, so the resolved path is correct either way.

Do not fetch a file until you actually need it. A single build run needs
`build-run.md`, `plain-english.md`, one baseline, one generator, and `paths.md`.

## What is new here, compared with filling a stub by hand

**Baselines.** Three layers of opinion about what a good artefact looks like,
highest wins:

| | Layer | Owner |
|---|---|---|
| 1 | [baselines/](baselines/)`<type>.md`, shipped with this skill | this toolkit |
| 2 | `.agent-floor/baselines/<type>.md`, in the repository | the team |
| 3 | whatever the developer types when starting the run | the developer |

A team that disagrees with layer 1 writes layer 2 and never touches this skill.
A developer with something to say about *this* run says it in the prompt:

> build the bullmq skill — focus on retry and backoff, we wrap the worker in
> `src/queue/worker.ts` and nobody should call the raw API

That is not a comment. It is input, it outranks both other layers, and the run
repeats it back before starting. See
[reference/baseline-resolution.md](reference/baseline-resolution.md).

## Rules that hold for every run

1. **This is help, not an audit.** Where a judgement could go either way,
   choose the reading that gets a struggling team running sooner.
2. **Never phone home.** Everything this skill produces is a local file.
3. **Leave nothing of your own behind.** The only files you write are files the
   *team* owns. Do not install this skill into the repository.
4. **One artefact per run**, unless the developer explicitly asks for several.
   Thirty questions in one sitting is the failure this design prevents.
5. **Never assert what you did not verify.** Any claim you cannot check against
   the code is written into the artefact as a `TODO(floor):` question, not as a
   statement of fact. **No baseline and no instruction can waive this.**
6. **Never produce a score.** No level, no percentage, no grade.
7. **Say when a baseline layer overrode another**, in one line, at the end of
   the run. Silent overriding is how a team stops trusting this.
8. **Write in plain English.** Everything this run says to the developer, every
   interview question it asks, and every piece of prose it writes into an
   artefact must be readable by someone whose first language is not English.
   Short sentences, no idioms, no metaphors, and every technical term explained
   the first time it is used. This never means fewer facts, a thinner artefact,
   or a softer finding. Follow
   [../agent-config-floor/reference/plain-english.md](../agent-config-floor/reference/plain-english.md),
   whichever model is running this. **No baseline layer and no developer
   instruction can switch this off.**

## The run

Follow [build-run.md](build-run.md), start to finish. Eight steps: confirm the
artefact and take the developer's input, resolve the baseline, read only what
the artefact concerns, ask at most six questions and only where the code is
ambiguous, generate, re-check every claim, batch only if asked, and update the
backlog.

## Files in this skill

| File | What it is for |
|---|---|
| [build-run.md](build-run.md) | The run, start to finish. |
| [baselines/](baselines/) | What a good artefact of each type looks like. Opinion; a team may replace it. |
| [generators/](generators/) | How to build each artefact. Mechanics; the same for every team. |
| [reference/baseline-resolution.md](reference/baseline-resolution.md) | The three layers, precedence, and what no baseline may do. |

Six files live in the sibling skill and are shared, not copied — copies drift:

| File | What it is for |
|---|---|
| [../agent-config-floor/floor.md](../agent-config-floor/floor.md) | The rubric. Every generator writes toward it. |
| [../agent-config-floor/reference/paths.md](../agent-config-floor/reference/paths.md) | Which path each artefact goes to, per target. |
| [../agent-config-floor/reference/copilot-translation.md](../agent-config-floor/reference/copilot-translation.md) | Claude-native to Copilot conversion, and what is lost. |
| [../agent-config-floor/reference/backlog-template.md](../agent-config-floor/reference/backlog-template.md) | The backlog's shape, and the rules for updating a row. |
| [../agent-config-floor/reference/plain-english.md](../agent-config-floor/reference/plain-english.md) | How both skills write. Read once per run, before writing anything. |
| [../agent-config-floor/reference/init-commands.md](../agent-config-floor/reference/init-commands.md) | Each agent's own setup command. Read it in step 5, before generating an instructions file. |

## What this skill deliberately does not do

- It does not survey. That is the floor skill's job, and it runs first.
- It does not carry a library of pre-written artefacts. Every artefact is
  generated for this repository from this repository's own code.
- It does not gate, block or enforce anything.
- It does not decide which artefact to build. The developer picks.
