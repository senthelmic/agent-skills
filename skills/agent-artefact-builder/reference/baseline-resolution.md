# Baseline resolution

A **baseline** says what a good artefact of one type looks like. It is opinion,
deliberately, and opinion belongs to whoever is closest to the work. So a
baseline arrives in three layers, and the layer closest to the work wins.

---

## The three layers

| | Layer | File | Who owns it |
|---|---|---|---|
| 1 | Shipped | `baselines/<type>.md`, beside this file's skill | This toolkit |
| 2 | Repository | `.agent-floor/baselines/<type>.md`, in the repository being built | The team |
| 3 | Invocation | Whatever the developer typed when starting the run | The developer, right now |

**Highest present layer wins on any point where they disagree.** Layers are not
replaced wholesale: a repository baseline that speaks only about naming leaves
every other point of the shipped baseline in force.

`<type>` is exactly one of:

`instructions`, `coding-standards`, `skill`, `code-review`, `saved-prompt`,
`sub-agent`

The same six the generators cover.

---

## Layer 1 — the shipped baseline

Always present. Read it first, every run. It is this toolkit's recommendation
for the artefact type, and for a team that has never thought about the question
it is a reasonable answer rather than no answer.

---

## Layer 2 — the repository baseline

Read `.agent-floor/baselines/<type>.md` if it exists. **Never write it, and
never create it uninvited.** It belongs to the team.

A repository baseline is ordinary markdown. There is no schema, because a schema
would be one more thing to learn before a team can express a preference. Read it
as instructions and apply them.

If a developer asks for one to be created, write what they dictate and nothing
more. Do not seed it with a copy of the shipped baseline — a copy drifts, and a
team that edits a copy loses every later improvement to the original.

**Say in the output that you used it**, naming the file. A team that wrote one
should see it take effect.

---

## Layer 3 — the invocation

Whatever the developer said when they started the run. This is the layer most
easily lost, because it arrives as ordinary prose mixed in with the artefact
name:

> build the bullmq skill — focus on retry and backoff, we wrap the worker in
> `src/queue/worker.ts` and nobody should call the raw API

Three separate instructions are in that sentence: a topic to emphasise, a file
to read, and a pattern to rule out.

**Extract it before you start, and repeat it back in one line.** The developer
can then correct you before any work is done, which is far cheaper than
correcting a finished artefact.

An instruction here is not a suggestion. If it contradicts both other layers, it
still wins — with one exception, below.

---

## What no baseline may do

**The floor is rubric, not taste.** These hold whatever any layer says, and a
developer asking for one of them to be waived is told plainly that it cannot be:

1. **The claim re-check always runs.** Every factual claim in a generated
   artefact gets its lookup. See [../build-run.md](../build-run.md) step 6.
2. **An unverifiable claim is never written as an assertion.** It becomes a
   `TODO(floor):` question. "Just write it, I'm sure it's right" does not change
   this — write it as the developer's stated answer and check it.
3. **Required frontmatter is written.** A skill or sub-agent without valid
   `name` and `description` fails criterion 2 and will not load. There is no
   style preference that survives a file the agent cannot read.
4. **No score, no grade, no percentage** appears in any output.
5. **The six-question cap holds.** A baseline may change *which* six, never how
   many.

Everything else is negotiable.

**Why these five and not others.** Each one is load-bearing for a promise made
to the team elsewhere: that this toolkit does not assert what it did not check,
that what it writes actually loads, that it invites rather than ranks, and that
a run stays small enough to finish. Style choices do not carry those promises,
so style is where a team's own judgement belongs.

---

## Reporting a conflict

When a higher layer overrode a lower one, **say so at the end of the run, in one
line per conflict**:

> Your repository baseline asks for examples drawn from tests; the shipped
> baseline prefers source files. Used tests.

Not a table, not a diff, not a section. One line, in plain words, naming which
layer won.

**Silent overriding is how a team stops trusting a tool.** A developer who
discovers months later that their baseline was quietly ignored — or quietly
obeyed in a way they did not intend — has no reason to write another one.

Do not report agreement. Only conflicts.

---

## A worked example

The developer runs:

> build the skill for our queue code — keep it under 30 lines

Resolution:

| Point | Layer 1 says | Layer 2 says | Layer 3 says | Result |
|---|---|---|---|---|
| Length | short, no fixed limit | — | under 30 lines | under 30 lines |
| Examples | from this repository, with paths | from tests | — | from tests |
| Frontmatter | `name` + `description` required | — | — | required, not negotiable |
| Unverified claims | become `TODO(floor):` | — | — | unchanged, not negotiable |

Reported at the end of the run:

> Kept the skill under 30 lines, as you asked. Your repository baseline asks for
> examples from tests, so the examples come from `src/queue/worker.test.ts`
> rather than from the source.
