# A build run

One artefact, one run. Opt-in, repeatable, and small enough that a developer
finishes it in one sitting.

Never build two artefacts without being asked. Thirty questions in a single
sitting is the failure this design exists to prevent. Batch mode exists, and is
opt-in — see step 7.

---

## Step 1 — confirm which artefact, and take the developer's input

**Two things arrive at the start of a run, and both matter.**

### The artefact

The developer picks one, from `.agent-floor/backlog.md` or from the unfilled
stubs in the file tree. If they have not picked, show the backlog's top rows and
ask. Do not choose for them.

Read the target from `.agent-floor/report.md` (the `target:` line in the YAML
header). If there is no report, the survey has not run — say so and point them
at the floor skill, listed in [SKILL.md](SKILL.md). Do not guess the target.

If the artefact they name is not in the backlog, that is fine. Build it, and add
a row for it at the end of the run.

### Whatever else they said

**Everything the developer typed alongside the artefact name is input to this
run.** It is the highest-precedence layer of the baseline stack, and it is easy
to lose because it arrives as ordinary prose:

> build the bullmq skill — focus on retry and backoff, we wrap the worker in
> `src/queue/worker.ts` and nobody should call the raw API

That sentence sets the scope of the read, names a convention, and rules out a
pattern. Extract it before you start, and **repeat it back in one line** so the
developer can correct you before any work is done. See
[reference/baseline-resolution.md](reference/baseline-resolution.md).

If they said nothing beyond the artefact name, that is normal. Do not ask them
to supply instructions they do not have.

---

## Step 2 — resolve the baseline

Before reading any code, resolve the baseline for this artefact type. Three
layers, highest wins:

1. `baselines/<type>.md` — ships with this skill
2. `.agent-floor/baselines/<type>.md` — this repository's override, if present
3. what the developer said in step 1

The rules, including what a baseline may never do, are in
[reference/baseline-resolution.md](reference/baseline-resolution.md). Read that
file once per run.

A baseline may widen or narrow what you read in step 3, and it shapes what you
write in step 5. It changes nothing else about this run.

---

## Step 3 — read only what this artefact concerns

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

The resolved baseline may adjust this scope. Anything the developer named
explicitly in step 1 is read, whatever the table says.

If the scoped directory is itself too large for one session, **sample it and say
in the output that you sampled**. Never read part of it and then speak as if you
had read all of it.

---

## Step 4 — interview, only where the code is ambiguous

**The questioning rule: ask only where the code is ambiguous.**

Read the directories the artefact concerns. Where the codebase disagrees with
itself — two files following different patterns, two styles of the same
framework, two ways of structuring a handler — surface the disagreement and ask
which one is the standard:

> `src/api/orders.ts` returns a bare object; `src/api/users.ts` wraps responses
> in `{ data }`. Which is the pattern to follow?

A repository that is internally consistent gets almost no questions. That is the
intended outcome, not a sign the run went wrong.

**Hard cap: six questions per build run.** If you find more than six
ambiguities, ask about the six that would most change what an agent writes, and
turn the rest into `TODO(floor):` lines in the artefact.

**Never ask what the developer already told you.** An answer supplied in step 1
is an answer. Asking again wastes one of the six and reads as not listening.

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

## Step 5 — generate

Use the generator for this artefact in [generators/](generators/). **One
generator per artefact, never one that writes everything** — a single prompt
covering instructions, skills, sub-agents and code review would be shallow at
all four.

Apply the baseline resolved in step 2 as you write.

**Prefer the vendor's own generator** where one reaches the developer's surface.
Claude Code has `/init`; VS Code has "Chat: Generate Instructions". The vendor
reads a codebase better than our prompt does. Route the developer to it, then
check and complete the result against the rubric in
[../agent-config-floor/floor.md](../agent-config-floor/floor.md) and against the
resolved baseline.

**Fall back to generating it yourself** where no vendor generator reaches that
surface — for example Copilot-only on JetBrains or Visual Studio. Reporting a
hole instead of filling it would fail the team.

Write to the path in
[../agent-config-floor/reference/paths.md](../agent-config-floor/reference/paths.md)
for the recorded target. Replace the stub; do not leave it alongside the real
file.

If the target is `both` and this artefact is not portable, generate the Copilot
side too, following
[../agent-config-floor/reference/copilot-translation.md](../agent-config-floor/reference/copilot-translation.md),
and mark the translation as best-effort in the output.

---

## Step 6 — re-check your own output

**Mandatory. This is the rule that makes self-grading safe. No baseline and no
instruction from the developer can switch it off.**

After generating, run the claim check from the floor skill's
[survey-run.md](../agent-config-floor/survey-run.md) step 3 against what you
just wrote. For every factual claim in the new artefact, do the one lookup that
confirms it.

- Claim confirmed → leave it as an assertion.
- Claim contradicted → fix it, then check again.
- Claim you cannot verify → **rewrite it as a `TODO(floor):` question to the
  developer.** Never leave it as an assertion.

---

## Step 7 — batch mode, only when asked

The default is one artefact. A developer may explicitly ask for several. When
they do:

1. **Confirm the list back**, in backlog order, with the number of questions you
   expect to ask in total. If that number is large, say so before starting —
   they may want fewer.
2. **Run them one at a time**, in full, including step 6 for each.
3. **Checkpoint after each artefact.** The file is written and the backlog row
   updated before the next one begins, so stopping halfway still leaves every
   finished artefact on disk.
4. **Stop at any point they ask**, and report what is done and what is not.

Never widen a single-artefact request into a batch because it seemed efficient.

---

## Step 8 — end the run

Update `.agent-floor/backlog.md`: set the row's `Status` to
`filled <YYYY-MM-DD>`. If the artefact had no row, append one. Follow the merge
rules in
[../agent-config-floor/reference/backlog-template.md](../agent-config-floor/reference/backlog-template.md).

Update the capability line in `.agent-floor/report.md` if this artefact changed
a capability's status. A file with a `TODO(floor):` marker anywhere in it still
counts as `absent` — see
[../agent-config-floor/floor.md](../agent-config-floor/floor.md), criterion 1.
Say that plainly, so a remaining `absent` does not read as the run having
failed.

Then tell the developer:

1. Which file was written, and which stub it replaced.
2. Any `TODO(floor):` lines still in it, and what each one is asking.
3. **Any place a higher baseline layer overrode a lower one**, in one line each.
   Silent overriding is how a team stops trusting this.
4. How many stubs are still unfilled, and how many backlog rows remain.
5. That the next one is a separate run, whenever they want it.

Then stop.
