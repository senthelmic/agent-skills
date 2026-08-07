# Generators

**One generator per artefact, never one that writes everything.** A single
prompt covering instructions, skills, sub-agents and code review would be
shallow at all four. Each generator here knows what its artefact is for, which
directories to read, and which ambiguities are worth asking about.

| Generator | Fills |
|---|---|
| [instructions.md](instructions.md) | capabilities 1 and 2 — build/test/run, what the project is and where code lives |
| [coding-standards.md](coding-standards.md) | capability 3 |
| [skill.md](skill.md) | capability 4 — one stack skill per run |
| [code-review.md](code-review.md) | capability 4 or 5 — the mandatory artefact, by name |
| [saved-prompt.md](saved-prompt.md) | capability 5 |
| [sub-agent.md](sub-agent.md) | capability 6 |

Every generator follows [../artefact-run.md](../artefact-run.md) for the run
shape: scoped read, at most six questions asked only where the code is
ambiguous, write what you have even if the interview is abandoned, then re-check
your own claims.

Every generator writes toward [../floor.md](../floor.md). Nothing here restates
the rubric; read it there.

## The rule that applies to all of them

**Prefer the vendor's own generator** where one reaches the developer's surface
— Claude Code's `/init`, VS Code's "Chat: Generate Instructions". The vendor
reads a codebase better than our prompt does. Generate it ourselves only where
no vendor generator reaches that surface. That fallback competes with nothing;
reporting a hole instead of filling it would fail the team.
