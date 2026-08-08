# Baseline: a saved prompt

What a good saved prompt looks like. Mechanics are in
[../generators/saved-prompt.md](../generators/saved-prompt.md); this file is
opinion, and a team may replace it — see [README.md](README.md).

---

## The one test

**Is there a real person who will type this more than once a month?**

If not, do not write it. A prompt nobody runs still has to be maintained, and it
goes stale silently — nobody notices a broken prompt they never run, so the one
time someone does run it, it misleads them.

Ask the developer directly if the repository does not make the answer obvious.
"Do not invent one to fill the capability" is the rule; this is how you avoid
breaking it.

---

## Worth a prompt, or not

| Worth it | Not worth it |
|---|---|
| Multi-step, with an order that matters | A single command with no arguments |
| Has preconditions people forget | Something the developer types from memory |
| Produces a file that must match a shape | A one-off |
| Done rarely enough that nobody remembers the steps | Done so often that everyone knows it |

The sweet spot is **rare enough to be forgotten, common enough to recur**. A
release procedure done monthly is the archetype. A test run done hourly is not.

---

## Point at files, do not restate them

The single most important rule for keeping a prompt alive.

| Weak | Strong |
|---|---|
| A copy of the endpoint boilerplate, inline | "Follow the shape of `src/api/orders.ts`, which is the most recently written endpoint." |
| A list of the fields a migration needs | "Copy the most recent file in `db/migrations/` and change the table name." |

A prompt that restates code is wrong the moment the code changes, and nothing
will tell you. A prompt that points at code stays correct for free.

---

## Steps in order, each one checkable

Write it as a sequence, not a paragraph. Each step should be something the agent
can tell whether it finished.

```markdown
1. Read `src/api/orders.ts` for the endpoint shape.
2. Create the new route file in `src/api/`.
3. Register it in `src/routes/index.ts` — a route file alone does nothing.
4. Add a test beside it as `<name>.test.ts`.
5. Run `pnpm test --filter api`.
```

Step 3 is the kind of line that makes a prompt worth having: it is the step
people forget, and forgetting it fails silently.

**End with a command wherever the task has one.** A prompt that ends by telling
the agent to check its own work is a prompt whose output can be trusted.

---

## The `description` is a routing signal

Same rule as skills. Write when to use it, not what it contains. It is what
makes the prompt findable in a list of twenty.

---

## Say the translation cost out loud

Saved prompts are not portable. Targeting both agents means this artefact is
authored once and translated once, and the translation loses things —
`$ARGUMENTS` is positional in Claude Code and named in Copilot; shell execution
with `!` has no equivalent.

**Tell the developer before starting**, not after. The doubled work should not
be a surprise, and a team targeting both agents may reasonably decide this
capability is not worth satisfying twice.

---

## What a reviewer would send back

- A prompt for a task nobody actually repeats.
- Code copied inline that could have been a path reference.
- Steps whose completion cannot be checked.
- A missing final command, where the task has an obvious one.
- A `description` that describes contents instead of triggers.
