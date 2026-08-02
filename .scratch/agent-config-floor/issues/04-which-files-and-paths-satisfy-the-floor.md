# Which files and paths satisfy the floor in both agents

Type: grilling
Status: open
Blocked by: 01

## Question

Once the floor's capabilities are settled, decide the concrete files and paths that satisfy each one across **both** Claude Code and GitHub Copilot — and how a check verifies them.

The constraint that makes this non-trivial is the instruction-file support matrix (see the map's standing constraints): `.github/copilot-instructions.md` reaches every Copilot surface, `AGENTS.md` reaches cloud agent plus VS Code chat plus code review, and `CLAUDE.md` reaches **Copilot cloud agent only**. Skills, by contrast, are already portable via `.claude/skills/`.

Decide:

- **Which file is the source of truth for instructions**, and how the others stay in sync — generated, symlinked, cross-referenced with a pointer, or duplicated and drift-checked. Each has a different failure mode; pick one and say why.
- **Whether the floor mandates a specific layout or accepts any layout that demonstrably loads.** Mandating is checkable but prescriptive; accepting-if-it-loads respects house style but is harder to verify.
- **How the check proves an agent actually reads the file**, rather than that a file exists at a path. Presence is trivially checkable and nearly worthless — a correctly named file in a location the tool ignores is precisely the invisible failure this effort exists to catch.
- **Whether `.github/skills`, `.claude/skills`, or `.agents/skills` is the floor's canonical skills location**, given all three resolve for Copilot but Claude Code has its own expectation.

Resolution is a path-level definition of the floor, plus the verification method for each item.
