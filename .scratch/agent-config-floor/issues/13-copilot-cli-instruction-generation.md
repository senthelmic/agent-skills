# Does Copilot CLI have an instruction-generating command

Type: research
Status: open
Blocked by: 05

## Question

[How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md) decided that the generate skill never writes instruction content itself — it routes the developer to their own vendor's generator. That leaves one gap: the only confirmed Copilot generator is VS Code's Command Palette command, so a Copilot-only team on Visual Studio, JetBrains, Xcode or github.com has nothing to route to.

Establish, with citations to primary GitHub and Microsoft documentation:

- **Does GitHub Copilot CLI have a command that generates `.github/copilot-instructions.md` from an existing codebase?** Name it exactly, or state plainly that none exists.
- **Is the VS Code command the only one?** Confirm or refute for Visual Studio, JetBrains, Xcode, and github.com. The ticket-05 answer asserts VS Code only; verify it rather than inheriting it.
- **If a generator exists outside VS Code, is it scriptable** — can the floor skill invoke it, or must a developer click it by hand? This decides whether the routing step can be automated or only instructed.

Resolution is a yes/no on Copilot CLI with the exact command, a corrected surface list, and a verdict on whether the "Copilot-only, not VS Code" gap closes.
