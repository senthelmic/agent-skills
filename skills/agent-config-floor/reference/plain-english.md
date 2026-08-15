# Plain English

**This file governs how both skills write. It is not a style preference — it is
a requirement of every run, and no baseline layer and no developer instruction
may switch it off.**

The person running a setup tool is often a new joiner, often working in a second
or third language, and often not the person who chose this technology stack.
Output they cannot read is output that did nothing.

---

## What this applies to

| Applies | Does not apply |
|---|---|
| Everything you say to the developer in the session | File paths, command names, flags |
| The prose in `.agent-floor/report.md` | Frontmatter keys and their values |
| The prose in `.agent-floor/backlog.md` | Code samples and code blocks |
| The prose inside every artefact you generate | The fixed schemas in the templates |
| Every question you ask in an interview | The status words `present`, `absent`, `unverifiable` |
| Every `TODO(floor):` line you write | Words the repository itself uses for its own things |

The fixed parts of a template are fixed. Where a template gives exact wording,
use that wording. Where it leaves you to write prose, this file governs it.

---

## The seven rules

### 1. One idea per sentence, and keep sentences short

Aim for under 20 words. A sentence with two commas and a semicolon is usually
two sentences.

- Write: "Claude Code cannot run your tests. No test command is written down."
- Not: "Claude Code, lacking any written record of a test command, is unable to
  run your tests without first asking you what the command is."

### 2. No idioms, no metaphors, no figures of speech

These are the hardest thing to read in a second language, because the words are
all common and the meaning is not in them.

| Do not write | Write |
|---|---|
| "low-hanging fruit" | "the easiest thing to fix first" |
| "moving the needle" | "making a real difference" |
| "out of the box" | "without any setup" |
| "a stub is not progress" | "a stub is an empty file. It does not count as done." |
| "this gates nothing" | "this does not block anything" |
| "bang for your buck" | "the most benefit for the least work" |

### 3. Define a technical term the first time you use it

Give the plain meaning in the same sentence, in brackets or after a dash. Then
use the term normally.

> A **stub** — an empty placeholder file — is written at the correct path for
> each artefact.

Terms that always need this on first use in a run: stub, artefact, frontmatter,
manifest, monorepo, greenfield, sub-agent, saved prompt, baseline, the floor.

### 4. Active voice, and name who does what

- Write: "Claude Code reads `CLAUDE.md`."
- Not: "`CLAUDE.md` is read by Claude Code."
- Write: "You need to run `/init`."
- Not: "`/init` should be run."

### 5. Use the plain word

| Do not write | Write |
|---|---|
| utilise, leverage | use |
| commence, initiate | start |
| terminate | stop, or end |
| facilitate | help |
| ascertain | find out |
| prior to | before |
| in the event that | if |
| a plethora of | many |
| non-trivial | hard, or slow |
| surface (as a verb) | show, or point out |

### 6. Never trade away accuracy for simplicity

Simple English does not mean fewer facts, a shorter list, or a softer finding.

- Keep every finding. Keep every backlog row. Keep every piece of evidence.
- Keep exact names exactly: `pnpm test --filter web` stays as it is.
- If a thing is genuinely complicated, use more short sentences. Do not use one
  complicated sentence, and do not drop the detail.

**A shorter report is not the goal. A readable one is.**

### 7. Say what to do next, in a full sentence

Every finding ends with the action, written as an instruction a person can
follow without asking a follow-up question.

- Write: "Run `/init` in Claude Code. It writes a `CLAUDE.md` file for you."
- Not: "Consider bootstrapping via the vendor init path."

---

## The check before you send

Read your output back and ask three questions:

1. Is there any sentence over 20 words that could be two sentences?
2. Is there any word here that a reader would have to look up, and that I did
   not explain when I first used it?
3. Did I lose a fact while making it simpler?

If the answer to 3 is yes, put the fact back. Rule 6 wins over rules 1 to 5.
