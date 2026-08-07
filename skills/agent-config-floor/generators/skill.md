# Generator: a stack skill

Fills capability 4. **One skill per run.** The developer picks which stub to
fill.

**Writes to** `.claude/skills/<name>/SKILL.md` (or `.github/skills/` when the
target is Copilot only). Skills need no translation — one file serves both
agents.

The mandatory code-review artefact has its own generator; see
[code-review.md](code-review.md).

---

## What a skill is for here

A skill captures **how this repository uses a technology**, not what the
technology is. The model already knows what TanStack Query is. What it does not
know is that this repository puts every query key in `src/queries/keys.ts` and
that a query written any other way will be rejected in review.

If, after reading, you have nothing to say that is specific to this repository,
**say so and do not write the skill.** A generic skill is worse than no skill:
it costs tokens on every session and teaches nothing. Tell the developer why,
and remove the stub.

---

## Step 1 — read, scoped by the skill

| Skill | Reads |
|---|---|
| Front-end / framework | the component directories |
| Backend / API | the API and handler layer |
| Database | schema, migrations, the data-access layer |
| A specific library | the files that import it |

Nothing else. If the scope is too large for one session, sample it and say in
the output that you sampled.

---

## Step 2 — ask, at most six

Only where the code disagrees with itself. Examples:

- Two ways of fetching data on the client, and no way to tell which is current.
- Two migration styles.
- Components in two different shapes, in the same directory.
- A wrapper around the library in some files and direct use in others.

Carry the evidence:

> `src/components/UserCard.tsx` uses `useQuery` directly; `src/components/OrderList.tsx`
> goes through `src/queries/useOrders.ts`. Should new code use the wrapper?

---

## Step 3 — write

```markdown
---
name: <kebab-case-name>
description: <when an agent should use this skill, in one sentence. This is the
  routing signal — write what triggers it, not what it contains.>
---

# <Name>

<One or two sentences: what this covers in this repository.>

## How we use it here

- <specific rule, with the path it applies to>
- <specific rule, with the path it applies to>

## Patterns to follow

<A short real example taken from this repository, with its path. Not invented
code.>

## What to avoid

<Only mistakes that are actually possible here. Not general warnings.>
```

Rules:

- **The `description` is the routing signal.** Write when to use it, not what is
  in it. "Use when writing or changing a data-fetching hook" beats "TanStack
  Query conventions".
- **Examples come from the repository**, with their path. Never invent example
  code and present it as the house pattern.
- Keep it short. This is loaded into context when it triggers.

---

## Step 4 — re-check

Every path, every import, every command in the skill: do the one lookup.
Anything you cannot verify becomes a `TODO(floor):` question, not an assertion.

Then confirm the frontmatter parses and has both `name` and `description`. A
skill with broken frontmatter fails criterion 2 and will not load.
