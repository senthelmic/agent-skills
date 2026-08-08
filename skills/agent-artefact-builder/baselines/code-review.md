# Baseline: the code-review artefact

What a good code-review artefact looks like. Mechanics are in
[../generators/code-review.md](../generators/code-review.md); this file is
opinion, and a team may replace it — see [README.md](README.md).

---

## Capture this team's review, do not import one

This artefact is mandatory in every repository. **Its content is not.**

There are good published review checklists. Do not use one. A checklist a team
did not write is a checklist a team does not follow, and the agent will raise
findings nobody in the team cares about — which is the fastest way to make
people stop reading agent reviews.

The right source, in order:

1. **What the team keeps saying in reviews.** Ask this directly; it is the
   single best question in the interview.
2. **What the git history shows breaking.** Fix commits clustered in one area
   name that area better than anyone's memory does.
3. **What the developer says needs care.** Even without a reason attached.

---

## The one test

**Would a reviewer here actually block a pull request over this?**

If not, it is not a review rule. It might be a convention — send it to the
conventions section, where it costs less and is loaded anyway.

A review artefact full of things nobody blocks over trains the agent to produce
findings nobody acts on.

---

## Aim for five to ten checks

Short enough to be read every time. A review list of thirty items is skimmed,
and a skimmed list is applied unevenly, which is worse than a short list applied
consistently.

If the team genuinely has thirty concerns, the top ten are the artefact and the
rest are `TODO(floor):` lines for someone to prune later.

---

## Name the sensitive areas by path

The most valuable section, and the one the code cannot supply on its own.

```markdown
## Areas that need extra care

- `src/billing/` — money. Changes need a second reviewer.
- `db/migrations/` — irreversible in production. Check the down migration exists.
- `src/auth/` — session handling. Do not change without a security review.
```

Each entry gives the path, the reason, and what to do differently. **The reason
matters as much as the path**: an agent that knows *why* an area is sensitive
can recognise a related risk the list did not anticipate.

---

## "I don't know" is expected here

More than in any other artefact. This is the most tribal of the six
capabilities, and the person running the setup is often a new joiner.

**Never leave this artefact unwritten because the answers are missing.** A file
of open questions at the right path is exactly the point:

```markdown
TODO(floor): what must never be merged without a test? Nobody available at
setup time knew the answer.
```

It is visible in the file tree, it appears in the next pull request that touches
it, and someone who knows will answer it. An empty stub achieves none of that,
because a stub says nothing about *what* is unknown.

---

## Commands come from the instruction file

If the pre-pull-request section names a command, take it from `CLAUDE.md` rather
than looking it up again. Two files that state the same command separately will
eventually state it differently, and nobody will notice which one is stale.

---

## What a reviewer would send back

- A generic review checklist that could apply to any repository.
- A sensitive area named with no reason attached.
- A check nobody would actually block a pull request over.
- A command restated instead of taken from the instruction file.
- An empty artefact because the interview produced no answers.
