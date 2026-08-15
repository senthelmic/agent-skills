# The setup commands

Each agent ships its own command that writes a first instructions file for a
repository. It is the cheapest thing a team can do, it takes about a minute, and
the vendor's own command reads a codebase better than any prompt of ours.

**A repository that has never had this run is the most common case this skill
meets.** So the survey run checks for it first, and the report says so first.

Checked against the vendor documentation on 2026-08-15. Version 1.0.0.

---

## What each command is

### Claude Code

| | |
|---|---|
| Command | `/init`, typed inside a Claude Code session |
| What it does | Reads the repository and writes a starter `CLAUDE.md` for it |
| File it writes | `CLAUDE.md`, at the root of the repository |

Two extras worth telling a team about:

- Setting the environment variable `CLAUDE_CODE_NEW_INIT=1` turns `/init` into a
  guided flow. It walks through skills, hooks and personal memory files as well
  as `CLAUDE.md`.
- If `/init` finds config files belonging to other coding agents, it offers to
  bring them across using `/import`.

### GitHub Copilot CLI

| | |
|---|---|
| Command | `copilot init` in the terminal, or `/init` inside a Copilot CLI session |
| What it does | "Initialize Copilot custom instructions and agentic features for this repository" |
| File it writes | Not stated in GitHub's command reference |

**Do not tell a team which file `copilot init` writes.** GitHub's own reference
does not say. Tell them to run it and then look at what changed in `git status`.

Copilot CLI reads instructions from several places and combines them. It does
not define a precedence order between them:

- `.github/copilot-instructions.md`
- `.github/instructions/**/*.instructions.md`
- `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`
- user-level files under `$HOME/.copilot/`, which are outside the repository and
  which this skill never checks

### GitHub Copilot in Visual Studio Code

| | |
|---|---|
| Command | `/init`, typed in the Copilot Chat box |
| What it does | Reads the workspace and writes always-on instructions for it |
| Related | `/create-instruction` writes one focused `*.instructions.md` file instead. The Command Palette entry is "Chat: New Instructions File". |

A team on Visual Studio or JetBrains has no equivalent command. For them, the
artefact builder skill writes the file instead.

---

## How the survey run checks this

**You cannot see whether someone typed a command.** You can only see what the
command leaves behind. Never write "you did not run `/init`" as a fact. Write
what you actually found.

| Target | Look for | Verdict |
|---|---|---|
| `claude` or `both` | `CLAUDE.md` at the repository root | see below |
| `copilot` or `both` | `.github/copilot-instructions.md`, or `.github/instructions/**/*.instructions.md`, or `AGENTS.md` | see below |

Every one of these files is already inside the step 1 reading budget, so this
check reads nothing new.

Three verdicts, and the exact wording to use:

| What you found | Verdict | Say |
|---|---|---|
| No such file at all | `not run` | "There is no `CLAUDE.md` here. That is the file `/init` writes, so it looks like the setup command has not been run yet." |
| The file exists but is empty, or is only a `TODO(floor):` stub | `not run` | "`CLAUDE.md` exists but has no real content in it yet." |
| The file exists with real content | `done` | Say nothing about setup commands. Go straight to the six capabilities. |

**A file that exists with real content ends the check.** Do not try to work out
whether a person wrote it or a command wrote it. It does not matter, and you
cannot tell.

---

## What this check does not do

- **It never counts toward the floor**, and it never changes any capability's
  status. It is one extra thing the report says, before the six capabilities.
- **It never runs the command for the developer.** Say which command to type,
  and let them type it. `/init` is a slash command inside an agent session; you
  cannot run it from a tool call anyway.
- **It never checks anything outside the repository.** Files under
  `$HOME/.copilot/` or `~/.claude/` belong to the person, not the team.
