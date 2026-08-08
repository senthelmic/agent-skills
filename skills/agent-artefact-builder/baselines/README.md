# Baselines

A baseline says **what a good artefact of one type looks like**. Six files, one
per artefact type.

| Baseline | For |
|---|---|
| [instructions.md](instructions.md) | `CLAUDE.md` / `.github/copilot-instructions.md` |
| [coding-standards.md](coding-standards.md) | the `## Conventions` section |
| [skill.md](skill.md) | a stack skill |
| [code-review.md](code-review.md) | the mandatory code-review artefact |
| [saved-prompt.md](saved-prompt.md) | a saved prompt |
| [sub-agent.md](sub-agent.md) | a sub-agent |

---

## Baseline or generator?

Two different things, kept in two different directories on purpose.

| | Generator | Baseline |
|---|---|---|
| Answers | *How* do I build this? | What makes it *good*? |
| Contains | which directories to read, which ambiguities to ask about, the output shape | length, tone, what to refuse to write, what a reviewer sends back |
| Kind of statement | mechanics | opinion |
| Can a team replace it? | No | Yes |

Where the two disagree about mechanics, the generator wins. Where they disagree
about taste, the baseline wins.

---

## How a team replaces one

Write `.agent-floor/baselines/<type>.md` in your own repository. Ordinary
markdown, no schema. It layers on top of the file here — it does not replace it
wholesale, so you only write the points you actually care about.

Anything you type when starting a run beats both.

Full rules: [../reference/baseline-resolution.md](../reference/baseline-resolution.md).

---

## What every baseline shares

These four hold in all six files, so none of them repeats them.

1. **Written for an agent, not for a person.** Every one of these artefacts is
   fed to an agent. Prose that reads well to a human but leaves the decision
   ambiguous is worse than a blunt rule.
2. **Every line costs tokens on every future session.** Length is not free.
   Cutting a line that teaches nothing is always correct.
3. **Specific beats complete.** Four true rules about this repository beat
   thirty generic ones. A generic line is not neutral — it dilutes the specific
   lines around it.
4. **Never assert what you did not check.** This is rubric, not taste, and no
   baseline may waive it. An unverifiable claim becomes a `TODO(floor):`
   question.

---

## These are opinions, and they are meant to be argued with

Nothing here is derived from measurement. They are the judgements this toolkit
makes in the absence of a team's own. A team that disagrees is not wrong — they
are doing exactly what layer 2 exists for.
