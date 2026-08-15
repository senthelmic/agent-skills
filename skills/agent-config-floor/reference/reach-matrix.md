# The Copilot reach matrix

**Versioned data this toolkit owns.** Verification is *computed reach*, never
bare file presence. A repository with everything in one file can look configured
and be invisible to half of Copilot — a silent failure this table exists to
catch.

- **Matrix version:** 2.0.0
- **Last verified against GitHub's published documentation:** 2026-08-14
- **Source:** GitHub Docs, *Support for different types of custom instructions*
  (`docs.github.com/en/copilot/reference/custom-instructions-support`). Full
  working, with every other source, in the effort's research file
  `copilot-formats.md`.

> **Staleness warning.** When GitHub changes this matrix, every verdict this
> skill has ever given silently becomes wrong until someone updates this file.
> If the date above is more than a few months old, say so in the report rather
> than presenting the reach conclusion as current fact. Who watches for changes
> is not yet owned.
>
> Version 1.0.0 of this file was transcribed from notes rather than read from
> the source, and was wrong in two ways: it had no column for path-specific
> instructions, and it treated `CLAUDE.md` as reaching less than `AGENTS.md`.
> Both are corrected here.

---

## The five instruction types

GitHub organises this by **type**, not by filename. Each type has its own
filenames.

| Type | Filename(s) | Does it count toward the floor? |
|---|---|---|
| **Repository-wide** | `.github/copilot-instructions.md` | Yes |
| **Path-specific** | `.github/instructions/**/*.instructions.md` | Yes |
| **Agent** | `AGENTS.md`, `CLAUDE.md` or `GEMINI.md` — all three identical | Yes |
| **Personal** | Editor UI settings; on the CLI, `~/.copilot/copilot-instructions.md` | **No** — not in the repository, so no teammate gets it |
| **Organization** | GitHub organization settings | **No** — same reason, and the team may not control it |

Only the first three are repository files. The floor is about what a repository
gives every person who clones it, so only those three are assessed.

---

## Instruction reach

`Y` = the surface reads this type. `—` = it does not.

| Environment | Feature | Repository-wide | Path-specific | Agent |
|---|---|---|---|---|
| GitHub.com | Copilot Chat | Y | — | — |
| GitHub.com | Copilot cloud agent | Y | Y | Y |
| GitHub.com | Copilot code review | Y | Y | Y |
| VS Code | Copilot Chat | Y | Y | Y |
| VS Code | Copilot cloud agent | Y | Y | Y |
| VS Code | Copilot code review | Y | — | — |
| Visual Studio | Copilot Chat | Y | Y | — |
| Visual Studio | Copilot code review | Y | — | — |
| JetBrains | Copilot Chat | Y | Y | — |
| JetBrains | Copilot cloud agent | Y | Y | Y |
| JetBrains | Copilot code review | Y | Y | — |
| Eclipse | Copilot Chat | Y | — | — |
| Eclipse | Copilot cloud agent | Y | Y | Y |
| Eclipse | Copilot code review | *no instruction support at all* | — | — |
| Xcode | Copilot Chat | Y | Y | — |
| Xcode | Copilot cloud agent | Y | Y | Y |
| Xcode | Copilot code review | Y | Y | — |
| Copilot CLI | All features | Y | Y | Y |

Eighteen rows. Count them when writing the report — say how many surfaces see
nothing, not how many files are missing.

**The four conclusions that follow from this table:**

1. **`.github/copilot-instructions.md` is the only entry supported by every row
   that supports anything.** This is why it is generated and committed whenever
   the target includes Copilot.
2. **`CLAUDE.md` and `AGENTS.md` now have identical Copilot reach.** They are
   one type. Do not report them differently.
3. **`CLAUDE.md` alone still leaves nine of eighteen rows blind** — GitHub.com
   Chat, VS Code code review, Visual Studio Chat and code review, JetBrains Chat
   and code review, Eclipse Chat, and Xcode Chat and code review. The config
   looks present. That is the silent failure, and it is still about half the
   surface area.
4. **`AGENTS.md` is dominated twice over.** `copilot-instructions.md` reaches
   strictly more, and `CLAUDE.md` matches it on the Copilot side while also
   being the only file Claude Code reads. Never ask for one; never penalise an
   existing one.

**Path-specific instructions count.** Thirteen rows read them — more than read
the Agent type. A repository that keeps its instructions in
`.github/instructions/**/*.instructions.md` is **well configured**, not below
the floor. Reporting otherwise is the worst error this tool can make.

Claude Code reads `CLAUDE.md`. It does not read
`.github/copilot-instructions.md` or `.github/instructions/`, so a Copilot-only
repository is invisible to Claude Code in the same way.

---

## Skills reach — the one portable capability

Copilot resolves skills from `.github/skills`, `.claude/skills` and
`.agents/skills`, using the same `SKILL.md` format as Claude Code. One directory
serves both agents with zero translation. Do not duplicate skills across paths.

**Required frontmatter: `name` and `description`.** `name` must be lowercase
with hyphens for spaces. A file missing either is not loaded — this applies to
stubs too.

Read by: Copilot cloud agent, Copilot code review, Copilot CLI, the GitHub
Copilot app, and agent mode in VS Code.

---

## Saved prompt reach — narrow, and in preview

`.github/prompts/<name>.prompt.md`.

Read by **VS Code, Visual Studio and JetBrains only.** Not github.com, not the
cloud agent, not code review, not Copilot CLI, not Eclipse, not Xcode.
Documented as **public preview and subject to change**.

Say this plainly when recommending one for a Copilot target. A team should know
they are authoring a second file that reaches three editor chat windows and may
move.

---

## Custom agent reach

`.github/agents/<name>.agent.md`. Only `description` is required in the
frontmatter.

Read by: Copilot cloud agent on github.com, Copilot CLI, and VS Code. In public
preview for JetBrains, Eclipse and Xcode.

`.chatmode.md` is the **old name for the same thing** and has been superseded.
An existing `.chatmode.md` still counts when auditing; never generate one, and
suggest renaming it to `.agent.md`.

---

## How to use this file in a report

Write what a surface **cannot do**, not what file is missing:

> Copilot Chat in Visual Studio and JetBrains sees nothing here, because
> instructions live only in `CLAUDE.md` and those surfaces do not read the agent
> instruction type.

Not:

> `.github/copilot-instructions.md` is missing.

If the target is `claude` only, do not report Copilot reach gaps at all. The
team chose their agents; reporting gaps for an agent they do not use is noise.
