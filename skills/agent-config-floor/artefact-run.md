# An artefact run

One artefact, one run. Opt-in, repeatable, and small enough that a developer
finishes it in one sitting.

Never run two of these without being asked. Thirty questions in a single sitting
is the failure this design exists to prevent.

---

## Step 1 — confirm which artefact

The developer picks one from the list of unfilled stubs. If they have not
picked, show the list and ask. Do not choose for them.

Read the target from `.agent-floor/report.md` (the `target:` line in the YAML
header). If there is no report, run the survey first.

---

## Step 2 — read only what this artefact concerns

**The artefact scopes the read, and nothing else does.** Nothing here is ever an
open-ended scan.

| Artefact | Reads |
|---|---|
| Front-end / framework skill | the component directories |
| Backend / API skill | the API and handler layer |
| Database skill | schema, migrations, the data-access layer |
| Coding standards | a sample across the directories the survey identified |
| Code-review artefact | the diff surface — recent commits and the files they touch |
| Project instructions | the manifests, the README, and the top-level directories |
| Saved prompt | the directories the prompt's task concerns |
| Sub-agent | the directories the sub-agent's task concerns |

If the scoped directory is itself too large for one session, **sample it and say
in the output that you sampled**. Never read part of it and then speak as if you
had read all of it.

---

## Step 3 — interview, only where the code is ambiguous

**The questioning rule: ask only where the code is ambiguous.**

Read the directories the artefact concerns. Where the codebase disagrees with
itself — two files following different patterns, two styles of the same
framework, two ways of structuring a handler — surface the disagreement and ask
which one is the standard:

> `src/api/orders.ts` returns a bare object; `src/api/users.ts` wraps responses
> in `{ data }`. Which is the pattern to follow?

A repository that is internally consistent gets almost no questions. That is the
intended outcome, not a sign the run went wrong.

**Hard cap: six questions per artefact run.** If you find more than six
ambiguities, ask about the six that would most change what an agent writes, and
turn the rest into `TODO(floor):` lines in the artefact.

### Evidence, never a bare default

Every pre-filled answer carries its evidence. Never a bare default, and never a
confidence score.

> Test command — I read `pnpm test` from the `scripts` block of `package.json`.
> Is that right?

### Rules

- **"I don't know" is an accepted answer** and becomes a `TODO(floor):` line.
  Assume the person running this is a **new joiner**, because that is who runs a
  setup tool. Never ask a question that needs history they may not have.
- **A half-finished interview always writes what it has.** Abandonment must
  never leave the repository worse than before. Every unanswered item becomes an
  explicit `TODO(floor):` line naming the open question.
- **Resuming is simply re-running this skill** and picking the same artefact. It
  reads what is already there and asks only what is still open.
- **Greenfield generates less on purpose.** With no code to read, write the
  structure plus `TODO(floor):` lines, and end the run by telling the developer
  to run it again once there is code. A thin honest file beats a rich wrong one,
  because this file is fed to an agent on every future session.

---

## Step 4 — generate

Use the generator for this artefact in [generators/](generators/). **One
generator per artefact, never one that writes everything** — a single prompt
covering instructions, skills, sub-agents and code review would be shallow at
all four.

**Prefer the vendor's own generator** where one reaches the developer's surface.
Claude Code has `/init`; VS Code has "Chat: Generate Instructions". The vendor
reads a codebase better than our prompt does. Route the developer to it, then
check and complete the result against [floor.md](floor.md).

**Fall back to generating it yourself** where no vendor generator reaches that
surface — for example Copilot-only on JetBrains or Visual Studio. Reporting a
hole instead of filling it would fail the team.

Write to the path in [reference/paths.md](reference/paths.md) for the recorded
target. Replace the stub; do not leave it alongside the real file.

If the target is `both` and this artefact is not portable, generate the Copilot
side too, following
[reference/copilot-translation.md](reference/copilot-translation.md), and mark
the translation as best-effort in the output.

---

## Step 5 — re-check your own output

**Mandatory. This is the rule that makes self-grading safe.**

After generating, run the claim check from
[survey-run.md](survey-run.md) step 3 against what you just wrote. For every
factual claim in the new artefact, do the one lookup that confirms it.

- Claim confirmed → leave it as an assertion.
- Claim contradicted → fix it, then check again.
- Claim you cannot verify → **rewrite it as a `TODO(floor):` question to the
  developer.** Never leave it as an assertion.

---

## Step 6 — end the run

Tell the developer:

1. Which file was written, and which stub it replaced.
2. Any `TODO(floor):` lines still in it, and what each one is asking.
3. How many stubs are still unfilled.
4. That the next one is a separate run, whenever they want it.

Update the capability line in `.agent-floor/report.md` if this artefact changed
a capability's status. A file with a `TODO(floor):` marker anywhere in it still
counts as `absent` — see [floor.md](floor.md), criterion 1.

Then stop.
