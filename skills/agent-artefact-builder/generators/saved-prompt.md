# Generator: a saved prompt

Fills capability 5. **One prompt per run.**

**Writes to** `.claude/commands/<name>.md`, and — when the target is `both` — a
translated copy at `.github/prompts/<name>.prompt.md`.

**Not portable.** Targeting both agents means this artefact is authored once and
translated once, and the translation is lossy. Say that to the developer before
starting, so the doubled work is not a surprise.

**Read [../baselines/saved-prompt.md](../baselines/saved-prompt.md) first.** This file is the
mechanics; the baseline is what a good one looks like, and the team may have
replaced it. See
[../reference/baseline-resolution.md](../reference/baseline-resolution.md).

---

## What a saved prompt is for

A task this team repeats, where typing the instructions again each time is
waste. Good candidates come from what the repository already shows:

- a release or changelog step described in the README or CI
- a scaffolding task — "add a new endpoint", "add a new component" — where the
  repository has an obvious repeated shape
- a check that runs before every pull request
- a migration or data task with a fixed sequence

**Do not invent a prompt to fill the capability.** If nothing in this repository
is genuinely repeated, ask the developer what they do repeatedly, and write
that. A prompt nobody runs is a file that costs maintenance and returns nothing.

---

## Step 1 — read

Only the directories the prompt's task concerns. If the prompt is "add a new
API endpoint", read the API directory and one existing endpoint end to end.

---

## Step 2 — ask, at most six

- Which repeated task should this cover? (Ask this if the developer has not
  already said.)
- Where the code shows two ways of doing that task, which one is the standard?
- What arguments does it need?

---

## Step 3 — write

```markdown
---
description: <what this prompt does, in one sentence>
argument-hint: <what to pass, if anything>
---

<The instructions, written as if speaking to the agent.>

<Reference the real files that show the pattern, by path.>

$ARGUMENTS
```

Rules:

- **Point at real files** rather than restating their content. The prompt stays
  correct as the code changes.
- Keep the steps in order and make each one checkable.
- If the task ends with a command, name the command literally.

---

## Step 4 — translate, if the target is `both`

Write `.github/prompts/<name>.prompt.md`, following
[../../agent-config-floor/reference/copilot-translation.md](../../agent-config-floor/reference/copilot-translation.md).

The main loss: `$ARGUMENTS` is positional in Claude Code and named in Copilot
(`${input:name}`). Invent a readable input name and say in the body what it
expects. Where a Claude feature has no Copilot equivalent — shell execution with
`!`, `argument-hint` — leave a `TODO(floor):` line naming what was lost.

Mark the translated file as best-effort with the header comment from the
translation reference.

---

## Step 5 — re-check

Every path and command in both files: one lookup each. Unverified claims become
`TODO(floor):` questions.
