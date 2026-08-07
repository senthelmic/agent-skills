# agent-skills

Agent skills for Claude Code and GitHub Copilot.

| Skill | What it does |
|---|---|
| [agent-config-floor](skills/agent-config-floor/) | Finds out what Claude Code and GitHub Copilot cannot do in a repository yet, and helps the team fill the gaps one artefact at a time. |

---

## agent-config-floor

The **floor** is the minimum agent configuration every repository should have.
It is a minimum, never a template.

The skill has two kinds of run:

- **The survey run** is cheap and bounded. It reads a fixed set of files,
  writes a report at `.agent-floor/report.md` saying what the agents cannot do
  in this repository yet, and leaves a one-line placeholder file (a **stub**) at
  the correct path for every artefact the repository should have.
- **An artefact run** is opt-in and fills exactly one of those stubs with real
  content. It reads only the directories that artefact concerns, asks at most
  six questions, and only where the code is ambiguous.

The team works through the stubs at whatever pace suits them. Nothing forces
them past the survey run.

There is no CLI, no npm package, no Node dependency, no server and no
telemetry. The only prerequisite is an agent.

### How to use it

Pick whichever of the three routes fits. They all run the same skill; they
differ only in how the agent gets the files.

#### 1. From a URL — the normal route

Paste this into Claude Code or GitHub Copilot, inside the repository you want
to set up:

> Fetch `https://raw.githubusercontent.com/senthelmic/agent-skills/main/skills/agent-config-floor/SKILL.md` and follow it.

The agent retrieves the skill and runs it. Nothing is installed.

Two things to know:

- Raw GitHub URLs are cached for about five minutes. If you push a change and
  still see the old version, wait, or add `?v=2` to the end of the URL.
- The skill is always the latest version. There is no pinning, on purpose: a
  team learns the floor moved by running the tool, never by being told.

#### 2. From a local clone — when the network is restricted

```sh
git clone --depth 1 https://github.com/senthelmic/agent-skills.git
```

Then, in the repository you want to set up:

> Read `<path>/agent-skills/skills/agent-config-floor/SKILL.md` and follow it.

#### 3. As an installed skill — when you want it to trigger by name

```sh
ln -s "$(pwd)/skills/agent-config-floor" ~/.claude/skills/agent-config-floor
```

Claude Code then loads it like any other skill and routes to it from the
description in its frontmatter. Remove it with `rm ~/.claude/skills/agent-config-floor`.

A symlink means edits in this repository take effect immediately, with no
copying, which makes this the best route while developing the skill.

### What it writes into your repository

- `.agent-floor/report.md` — the report. You choose whether to commit it. It
  never counts toward the floor.
- One stub per missing artefact, at the path that artefact belongs at.
- On an artefact run, the real artefact, replacing its stub.

That is all. The skill does not install itself into your repository, and it
never sends anything anywhere.

### What it deliberately does not do

- It does not gate, block or enforce anything. There is no CI check.
- It does not give a score, a level or a percentage.
- It does not carry a library of pre-written skills. Every artefact is
  generated for your repository, from your repository's own code.
- It does not measure token spend or response quality. That is a later phase.

### Status

Phase 1 (Bootstrap), floor version 1.0.0. The spec and the decisions behind it
are in `.scratch/agent-config-floor/`.
