# The Copilot reach matrix

**Versioned data this toolkit owns.** Verification is *computed reach*, never
bare file presence. A repository with everything in `CLAUDE.md` looks configured
and is invisible to several Copilot surfaces — a silent failure this table
exists to catch.

- **Matrix version:** 1.0.0
- **Last verified against GitHub's published documentation:** 2026-08-07
- **Source:** GitHub Docs, "Adding repository custom instructions for GitHub
  Copilot", and the Copilot CLI documentation.

> **Staleness warning.** When GitHub changes this matrix, every verdict this
> skill has ever given silently becomes wrong until someone updates this file.
> If the date above is more than a few months old, say so in the report rather
> than presenting the reach conclusion as current fact. Who watches for changes
> is not yet owned — see the effort's map.

---

## Instruction-file reach

`Y` = the surface reads this file. `—` = it does not.

| Surface | `.github/copilot-instructions.md` | `AGENTS.md` | `CLAUDE.md` |
|---|---|---|---|
| Copilot coding agent (cloud) | Y | Y | Y |
| Copilot Chat — VS Code | Y | Y | — |
| Copilot Chat — Visual Studio | Y | — | — |
| Copilot Chat — JetBrains | Y | — | — |
| Copilot Chat — Xcode | Y | — | — |
| Copilot Chat — github.com | Y | — | — |
| Copilot CLI | Y | Y | Y |
| Copilot code review | Y | Y (github.com only) | — |

**The three conclusions that follow from this table:**

1. **`.github/copilot-instructions.md` is the only path that reaches every
   Copilot surface.** This is why it is generated and committed whenever the
   target includes Copilot.
2. **`AGENTS.md` is dominated.** Everything it reaches is reached by
   `copilot-instructions.md`, and it misses four Chat surfaces. It is therefore
   never asked for — but an existing one still counts for what it reaches.
3. **`CLAUDE.md` alone leaves a team invisible** to Copilot Chat in Visual
   Studio, JetBrains, Xcode and github.com, and to Copilot code review
   everywhere. The config looks present. That is the silent failure.

Claude Code reads `CLAUDE.md`. It does not read
`.github/copilot-instructions.md`, so a Copilot-only repository is invisible to
Claude Code in the same way.

---

## Skills reach

Skills are the one portable capability. Copilot resolves skills from
`.github/skills`, `.claude/skills` and `.agents/skills`, using the same
`SKILL.md` format and frontmatter as Claude Code. One directory serves both
agents with zero translation. Do not duplicate skills across paths.

---

## Saved prompts and sub-agents

Not portable. Each must be authored once per agent. Formats and placeholder
syntax differ; the conversion is lossy. See
[copilot-translation.md](copilot-translation.md).

---

## How to use this file in a report

Write what a surface **cannot do**, not what file is missing:

> Copilot Chat in Visual Studio and JetBrains sees nothing here, because
> instructions live only in `CLAUDE.md` and those surfaces do not read it.

Not:

> `.github/copilot-instructions.md` is missing.

If the target is `claude` only, do not report Copilot reach gaps at all. The
team chose their agents; reporting gaps for an agent they do not use is noise.
