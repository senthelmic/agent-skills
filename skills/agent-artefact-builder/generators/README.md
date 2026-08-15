# Generators

**One generator per artefact, never one that writes everything.** A single
prompt covering instructions, skills, sub-agents and code review would be
shallow at all four. Each generator here knows what its artefact is for, which
directories to read, and which ambiguities are worth asking about.

| Generator | Fills | Baseline |
|---|---|---|
| [instructions.md](instructions.md) | capabilities 1 and 2 — build/test/run, what the project is and where code lives | [instructions](../baselines/instructions.md) |
| [coding-standards.md](coding-standards.md) | capability 3 | [coding-standards](../baselines/coding-standards.md) |
| [skill.md](skill.md) | capability 4 — one stack skill per run | [skill](../baselines/skill.md) |
| [code-review.md](code-review.md) | capability 4 or 5 — the mandatory artefact, by name | [code-review](../baselines/code-review.md) |
| [saved-prompt.md](saved-prompt.md) | capability 5 | [saved-prompt](../baselines/saved-prompt.md) |
| [sub-agent.md](sub-agent.md) | capability 6 | [sub-agent](../baselines/sub-agent.md) |

Every generator follows [../build-run.md](../build-run.md) for the run
shape: scoped read, at most six questions asked only where the code is
ambiguous, write what you have even if the interview is abandoned, then re-check
your own claims.

Every generator writes toward the rubric in
[../../agent-config-floor/floor.md](../../agent-config-floor/floor.md). Nothing
here restates the rubric; read it there.

## Generators and baselines are different things

A **generator** is mechanics: which directories to read, which ambiguities are
worth a question, what shape the output file takes. It is the same for every
team.

A **baseline** is taste: what makes a *good* artefact of this type, what to
refuse to write, what a reviewer would send back. It is opinion, and a team may
replace it.

The generator is always followed. The baseline is resolved through three layers
— see [../reference/baseline-resolution.md](../reference/baseline-resolution.md).
Where a baseline and a generator disagree about mechanics, the generator wins.
Where they disagree about taste, the baseline wins.

**A baseline can never lower the floor.** It cannot switch off the claim
re-check, authorise an unverified assertion, or drop required frontmatter.

## The rule that applies to all of them

**Prefer the vendor's own generator** where one reaches the developer's surface
— Claude Code's `/init`, Copilot CLI's `copilot init`, VS Code Copilot Chat's
`/init`. See
[../../agent-config-floor/reference/init-commands.md](../../agent-config-floor/reference/init-commands.md).
The vendor reads a codebase better than our prompt does. Generate it ourselves
only where no vendor generator reaches that surface. That fallback competes with
nothing; reporting a hole instead of filling it would fail the team.

**Every generator writes prose in plain English**, following
[../../agent-config-floor/reference/plain-english.md](../../agent-config-floor/reference/plain-english.md).
That covers the artefact's own body text and every `TODO(floor):` line in it.
Code samples, paths, commands and frontmatter keys are left exactly as they are.
