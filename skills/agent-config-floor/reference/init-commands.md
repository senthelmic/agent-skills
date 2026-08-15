# The setup commands

Each agent has a command that writes a first instructions file for a repository.
It reads the code first, so what it writes is about that project. It takes about
a minute. It is the cheapest thing a team can do, and the vendor's own command
reads a codebase better than any prompt of ours.

**A repository where this has never been run is the most common case this skill
meets.** So the survey run checks for it first, and the report says it first.

---

## You already have these answers

**The setup commands are settled here. Do not look them up, and do not answer
them from memory.** That applies to the commands themselves, what each one
writes, and which surfaces have none — nothing wider.

Two reasons. Many teams run this skill inside a network that blocks web access,
and this check must work the same for them. And a search costs time and returns
blog posts alongside vendor documentation, which is how a guess ends up stated
as fact.

If something about the setup commands is not written in this file, it is not
something you tell the developer. Do not fill the gap from memory and do not go
and find it.

**This is not a rule against searching the web.** It is one topic that is
already answered. Elsewhere in a run, use the web where a run genuinely needs it
and where nothing local can answer the question — for example an unfamiliar
library the team depends on. Two conditions hold whenever you do:

- **Search only when it is needed**, never to double-check something you already
  have from the repository or from this toolkit's own files.
- **Never let the run depend on it.** If the search fails or the network is
  blocked, write `could not determine` and carry on. A run must finish offline.

- Checked against the vendors' published documentation on **2026-08-15**.
- File version **2.0.0**.

If that date is more than a few months old, the commands may have changed. Say
that plainly in the report only if a command you named does not appear to exist.
Do not go looking for a newer answer mid-run.

---

## The verified table

| Where the person works | Command | What it writes |
|---|---|---|
| Claude Code | type `/init` in the session | `CLAUDE.md` at the repository root |
| Copilot Chat in VS Code | type `/init` in the chat box | `.github/copilot-instructions.md` |
| Copilot in a terminal | run `copilot init` | an instructions file — **the exact path is not published, so never name one** |
| Copilot in Visual Studio, JetBrains, Eclipse or Xcode | **no such command exists** | — |

That last row matters. A person using Copilot inside Visual Studio has no setup
command at all. Never give them one. Send them to the builder skill instead,
which writes the file for them.

Two extras for Claude Code, worth one line each if the developer asks:

- Setting `CLAUDE_CODE_NEW_INIT=1` turns `/init` into a guided flow that also
  covers skills, hooks and personal memory files.
- If `/init` finds another coding agent's config files, it offers to bring them
  across with `/import`.

---

## The exact wording to use

Use these. Do not rewrite them, and do not add to them.

### Claude Code

> Claude Code can write this file for you. Open Claude Code in this folder and
> type `/init`. It reads your code and writes a `CLAUDE.md` file about your
> project. This takes about a minute.

### GitHub Copilot

Show all three lines. Do not ask the person which one applies — let them pick
the line that matches them.

> Copilot can write this file for you. It reads your code first, so what it
> writes is about your project. Pick the line that matches how you use Copilot:
>
> - **In VS Code** — click in the Copilot Chat box and type `/init`
> - **In a terminal** — type `copilot init`
> - **In Visual Studio, JetBrains, Eclipse or Xcode** — Copilot has no command
>   for this yet. The builder skill can write the file for you instead.
>
> This takes about a minute.

### The closing line, after either block

> When it has finished, run this survey again to see what changed.

That line is why you never need to name the file for the terminal route. The
next run finds whatever appeared and reports it.

---

## Four things never to say

These are the failures this file exists to prevent. Each one has been seen in
real output.

| Never write | Why | Write instead |
|---|---|---|
| "GitHub's own reference does not say which file it writes" | This is your research problem, not theirs. It also makes the tool sound unsure. | Nothing. Leave the file unnamed. |
| "Run `git status` to see which file it wrote" | This makes the person do your checking for you, and a beginner may not use git directly. | "When it has finished, run this survey again to see what changed." |
| "In your terminal, run `copilot init`" *(alone)* | It assumes they use a terminal. Many people only ever use Copilot inside an editor. | The three-line block above. |
| "You did not run `/init`" | You cannot see what a person typed. You can only see files. | "There is no `CLAUDE.md` file here. That is the file `/init` writes." |

The general rule behind all four is in
[plain-english.md](plain-english.md): **never explain your own working.**

---

## How the survey run checks this

**You cannot see whether someone typed a command.** You can only see what the
command left behind. State what you found, never what they did.

| Target | Look for |
|---|---|
| `claude` or `both` | `CLAUDE.md` at the repository root |
| `copilot` or `both` | `.github/copilot-instructions.md`, or any `.github/instructions/**/*.instructions.md`, or `AGENTS.md` |

Every one of these files is already inside the step 1 reading budget, so this
check reads nothing new.

| What you found | Verdict | Say |
|---|---|---|
| No such file at all | `not run` | "There is no `CLAUDE.md` file here. That is the file `/init` writes." Then the wording block above. |
| The file exists but is empty, or holds only a `TODO(floor):` stub | `not run` | "`CLAUDE.md` exists but has nothing in it yet." Then the wording block above. |
| The file exists with real content | `done` | Say nothing about setup commands at all. Go straight to the six capabilities. |

**A file that exists with real content ends the check.** Do not try to work out
whether a person wrote it or a command wrote it. It does not matter, and you
cannot tell.

---

## What this check does not do

- **It never counts toward the floor**, and it never changes any capability's
  status. It is one extra thing the report says, before the six capabilities.
- **It never runs the command for the developer.** Every command in the table
  runs inside their own agent session or their own terminal. You cannot type it
  for them. Say which one to type, and stop.
- **It never checks anything outside the repository.** Files under
  `$HOME/.copilot/` or `~/.claude/` belong to the person, not the team.
