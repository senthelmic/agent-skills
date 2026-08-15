# agent-skills

Agent skills for Claude Code and GitHub Copilot.

| Skill | What it does |
|---|---|
| [agent-config-floor](skills/agent-config-floor/) | Surveys a repository and reports what Claude Code and GitHub Copilot cannot do in it yet. Writes stubs and a ranked backlog. |
| [agent-artefact-builder](skills/agent-artefact-builder/) | Builds one of those artefacts properly, using your own baselines and your own instructions. |

The two run in order: survey first, build second, one artefact at a time. They
are separate skills because the survey must stay cheap and the build must stay
small, and a single skill doing both drifts toward doing neither well.

There is no CLI, no npm package, no Node dependency, no server and no
telemetry. The only prerequisite is an agent.

---

## agent-config-floor

The **floor** is the minimum agent configuration every repository should have.
It is a minimum, never a template.

The survey run is cheap and bounded. It reads a fixed set of files and writes
three things:

| Output | What it is |
|---|---|
| Stubs | A one-line placeholder file at the correct path for every artefact the repository should have. Capped at five beyond the floor artefacts, so your file tree does not fill with placeholders. |
| `.agent-floor/report.md` | What the agents cannot do in this repository yet. No score, no grade. |
| `.agent-floor/backlog.md` | **The exhaustive list** — every artefact worth building here, of every type, ranked, each with the evidence that produced it. Not capped. |

The cap on stubs is about your file tree, not about what you are told. Anything
above the cap is in the backlog with its evidence, and filling a stub frees a
slot for the next survey.

Before all three, the survey checks one thing: has each agent's own setup
command been run here? Claude Code has `/init`, and GitHub Copilot has
`copilot init`. Each one reads your repository and writes a first instructions
file for you, in about a minute. If that file is not here, the report says so at
the top, because it is the cheapest fix available and it can move three of the
six capabilities on its own.

The team works through the backlog at whatever pace suits them. Nothing forces
them past the survey run.

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
ln -s "$(pwd)/skills/agent-config-floor"     ~/.claude/skills/agent-config-floor
ln -s "$(pwd)/skills/agent-artefact-builder" ~/.claude/skills/agent-artefact-builder
```

Claude Code then loads them like any other skill and routes from the description
in their frontmatter.

**Link both, not one.** The builder reads six shared files from
`../agent-config-floor/`, which only resolves if both sit side by side.

A symlink means edits in this repository take effect immediately, with no
copying, which makes this the best route while developing the skills.

### What it writes into your repository

- `.agent-floor/report.md` — the report. You choose whether to commit it. It
  never counts toward the floor.
- `.agent-floor/backlog.md` — the exhaustive list. Same: yours to commit or not,
  and it never counts toward the floor. Add your own rows; a later survey merges
  rather than replaces, so nothing you write is lost.
- One stub per missing artefact, at the path that artefact belongs at.

That is all. The skill does not install itself into your repository, and it
never sends anything anywhere.

### What it deliberately does not do

- It does not gate, block or enforce anything. There is no CI check.
- It does not give a score, a level or a percentage.
- It does not carry a library of pre-written skills. Every artefact is
  generated for your repository, from your repository's own code.
- It does not measure token spend or response quality. That is a later phase.

---

## agent-artefact-builder

Fills in **one** artefact per run — a skill, a saved prompt, a sub-agent,
project instructions, coding standards, or the code-review artefact. It reads
only the directories that artefact concerns, asks at most six questions, and
only where the code disagrees with itself.

Run the floor skill first. This one reads its report and its backlog.

### How to use it

Same three routes as above, with `agent-artefact-builder` in place of
`agent-config-floor`. Then say which artefact you want:

> Build the `bullmq` skill from the backlog.

### Telling it how you want it built

This is the part worth knowing about. **Three layers of opinion, highest wins.**

| | Layer | Where | Who owns it |
|---|---|---|---|
| 1 | Shipped baseline | `skills/agent-artefact-builder/baselines/<type>.md` | this repository |
| 2 | Your baseline | `.agent-floor/baselines/<type>.md` in **your** repository | you |
| 3 | What you type | the prompt that starts the run | you, right now |

`<type>` is one of `instructions`, `coding-standards`, `skill`, `code-review`,
`saved-prompt`, `sub-agent`.

**Layer 1** is this toolkit's opinion about what a good artefact of that type
looks like — how long, what the `description` must do, what to refuse to write,
what a reviewer would send back. Read them; they are meant to be argued with.

**Layer 2** is yours. Drop a markdown file at
`.agent-floor/baselines/skill.md` in your repository and it layers on top. No
schema — write it as instructions. You only write the points you care about; the
rest of layer 1 stays in force. The builder never writes this file for you.

**Layer 3** is whatever you type when you start the run:

> Build the `bullmq` skill — focus on retry and backoff, we wrap the worker in
> `src/queue/worker.ts` and nobody should call the raw API.

That is not a comment. It is input, it beats both other layers, and the run
repeats it back to you before doing any work so you can correct it early.

**When a higher layer overrides a lower one, the run says so in one line at the
end.** Silent overriding is how you stop trusting a tool.

### What no baseline can change

Six things are rubric rather than taste, and no layer can waive them:

- every factual claim is checked against the code before it is written
- a claim that cannot be checked becomes a `TODO(floor):` question, never an
  assertion
- required frontmatter is always written — a file the agent cannot load is not a
  style choice
- no score, grade or percentage appears anywhere
- at most six questions per run
- everything is written in plain English — see below

---

## Plain English, in both skills

Both skills write for someone whose first language is not English. Short
sentences, no idioms, no metaphors, and every technical term explained the first
time it is used. That covers what the agent says to you in the session, the
report, the backlog, every question it asks, and the artefacts it writes.

It does **not** cover file paths, command names, code samples or frontmatter
keys. Those stay exactly as they are.

**Simple wording never means fewer facts.** The rule is that a finding must be
readable, not that it must be short or soft. Where something is genuinely
complicated, the skills use more short sentences rather than dropping the
detail.

This holds whichever model runs the skill, and no baseline layer and no
instruction you type can switch it off. The rules are in
[skills/agent-config-floor/reference/plain-english.md](skills/agent-config-floor/reference/plain-english.md).

Everything else is negotiable.

---

## Status

Phase 1 (Bootstrap), floor version 1.0.0. The spec and the decisions behind it
are in `.scratch/agent-config-floor/` and `.scratch/agent-artefact-builder/`.
