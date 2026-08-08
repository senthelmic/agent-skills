# Baseline: a sub-agent

What a good sub-agent looks like. Mechanics are in
[../generators/sub-agent.md](../generators/sub-agent.md); this file is opinion,
and a team may replace it — see [README.md](README.md).

---

## The one test

**Does this work read a lot and return a little?**

That is the whole economic case for a sub-agent. It burns its own context
window on reading, and hands back a small answer, leaving the main session's
context intact.

Work that reads a little and returns a lot is worse as a sub-agent than as part
of the main session: you pay for the handoff and get nothing back.

| Good | Bad |
|---|---|
| "Find every place we call the payments API" → a list of paths | "Write this function" → the function |
| "Review this migration for problems" → a short verdict | "Refactor this file" → the whole file |
| "Which tests cover `src/auth/`?" → a list | "Explain this codebase" → an essay |

---

## Do not invent one to fill the capability

This is the capability most often satisfied with something nobody uses.

A small repository genuinely may not need one. The main session handles
everything, and a sub-agent adds a handoff for no gain. When that is the case,
**say so, and ask the developer what task they keep wanting to delegate.** Write
that one. If there is no answer, leave the stub and write down why — an honest
gap is better than a file that exists to make a check pass.

---

## Restrict `tools` deliberately

A read-only agent with write tools is a read-only agent in name only.

Decide from what the agent returns:

| The agent | Gets |
|---|---|
| Searches, explores, answers questions | read and search tools only |
| Reviews, gives a verdict | read and search tools only |
| Writes tests, writes docs | read, search, and write |

**Default to read-only.** It is easy to widen later, and a sub-agent that
unexpectedly writes files is the kind of surprise that makes a team switch the
whole thing off.

---

## Say what it returns, precisely

The most commonly missing part, and the one that decides whether the sub-agent
is worth having.

```markdown
## What to return

A list of file paths with one line each on how the payments API is called there.
Do not return the file contents. Do not summarise the payments API itself.
```

Both negatives matter. **A sub-agent that returns everything it read has
defeated its own purpose** — the main session now holds all that context anyway,
plus the cost of the handoff.

Name the format: a list, a verdict, a table, a count. "Report your findings" is
not a format.

---

## Scope it to paths

An unscoped sub-agent reads until it runs out of room. Name the directories it
works in, by path, in the body.

If the honest scope is the whole repository, that is usually a sign the task is
too broad to be one sub-agent. Split it.

---

## The `description` decides whether it is ever used

Same rule as skills and saved prompts: write the trigger, not the contents.

A sub-agent with a poor description is never delegated to, and its cost is
invisible — nobody notices work that does not happen.

---

## Say plainly that the Copilot copy may never trigger

Sub-agents are not portable. `name`, `description` and the body carry over, but
**Copilot routes differently**, and a translated file may simply never fire.

Tell the developer that, rather than implying the two behave the same. A team
that believes their sub-agent works on both agents, and finds out otherwise
after relying on it, has been misled by us.

---

## What a reviewer would send back

- A sub-agent whose work returns more than it reads.
- Write tools on an agent that only needs to read.
- A "what to return" section with no named format.
- No path scope, or the whole repository as the scope.
- A `description` that describes contents instead of triggers.
- A translated Copilot copy presented as equivalent.
