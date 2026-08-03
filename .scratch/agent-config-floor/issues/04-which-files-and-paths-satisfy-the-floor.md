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

**Added after the floor was settled — the duplication problem.** The floor's six capabilities split three ways, and only one way is free:

- **Instructions** (items 1–3) — one logical thing, several files with different reach. The asymmetry above.
- **Skills** (item 4) — genuinely portable. One `SKILL.md` serves both agents. Nothing to solve.
- **Saved prompts and sub-agents** (items 5–6) — **not portable at all.** Copilot `.prompt.md` and custom agents versus Claude Code slash commands and sub-agents: different formats, different directories. Every one of these must be authored twice and kept in sync forever, and the floor is what creates that burden.

So decide additionally:

- **Does one source generate both formats, or does a team maintain two copies by hand?** Hand-maintained copies drift, and drift is invisible until an agent behaves differently in one tool. Generation needs a source format that is nobody's native format, which is its own cost.
- **Is `Ruler` the answer here?** The prior-art research parked it as "revisit if the floor needs a distribution mechanism" — it now does. Re-read that section before deciding. Note its two disqualifiers found there: it maps Copilot to `AGENTS.md` rather than `.github/copilot-instructions.md`, and it gitignores generated config, which would hide the files from the Copilot surfaces that read the committed repo.
- **Or does the floor accept either format and not require both?** Cheapest option, and it means a repo can be at the floor for Claude Code but not Copilot. Decide whether that is acceptable or defeats the purpose.

Resolution is a path-level definition of the floor, plus the verification method for each item, plus the sync strategy for the two non-portable items.
