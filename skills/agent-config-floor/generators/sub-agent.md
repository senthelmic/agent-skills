# Generator: a sub-agent

Fills capability 6. **One sub-agent per run.**

**Writes to** `.claude/agents/<name>.md`, and — when the target is `both` — a
translated copy at `.github/agents/<name>.md`.

**Not portable.** Same doubling as saved prompts, and the routing behaviour
differs between the two agents even when the file translates cleanly.

---

## What a sub-agent is for

A task worth handing to a separate agent with its own context: work that reads a
lot and returns a little, or work that needs a different set of tools from the
main session.

Good candidates, judged from what the repository shows:

- a read-only search or exploration agent, in a large repository
- a test-writing agent scoped to one part of the codebase
- a review agent scoped to a sensitive area (migrations, auth, billing)
- a documentation agent for a directory with heavy public API surface

**Do not invent one to fill the capability.** If the repository is small enough
that the main session handles everything, say so, and ask the developer whether
there is a task they keep wanting to delegate. Write that one, or leave the stub
and say why.

---

## Step 1 — read

Only the directories the sub-agent's task concerns.

---

## Step 2 — ask, at most six

- Which task should this handle?
- Should it be able to write files, or read only?
- Which part of the codebase is it scoped to?

---

## Step 3 — write

```markdown
---
name: <kebab-case-name>
description: <when to hand work to this agent. This is the routing signal —
  write the trigger, not the contents.>
tools: <only the tools it needs. A read-only agent should not get write tools.>
---

<The system prompt: what this agent is for, where it works, and what it returns.>

## Scope

<The directories it works in, by path.>

## What to return

<What the main session gets back. Be specific — a sub-agent that returns
everything it read defeats the purpose.>
```

Rules:

- **The `description` decides whether it is ever used.** Write the trigger.
- **Restrict `tools` deliberately.** A read-only agent with write tools is a
  read-only agent in name only.
- **Say what it returns.** The whole value is that it reads a lot and returns a
  little.

---

## Step 4 — translate, if the target is `both`

Write `.github/agents/<name>.md`, following
[../reference/copilot-translation.md](../reference/copilot-translation.md).

`name`, `description` and the body carry over. Tool names differ — drop what has
no counterpart and note the drop. Drop `model`; there is no reliable equivalent.
Do not assume the same delegation happens: Copilot routes differently, and the
translated file may simply never trigger. Say that to the developer rather than
implying the two behave the same.

---

## Step 5 — re-check

Every path in both files: list it. Confirm the frontmatter parses and has `name`
and `description`. Anything unverified becomes a `TODO(floor):` question.
