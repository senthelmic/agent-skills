# What capabilities make up the floor

Type: grilling
Status: resolved

## Question

What must a repo have before we call it "at the floor"?

Settle the list as **capabilities**, not files — the file question is a separate ticket, because the same capability lands in different paths for each agent. Candidates from the original framing: project instructions, coding standards, at least one skill, agents, prompts. Which of those are genuinely floor, and which are house style?

Pressure to apply:

- **The floor has to be reachable by a team that is currently at zero.** Every item added raises the cost of clearing the bar and lowers the chance a struggling team ever does. What is the smallest set that still meaningfully changes agent behaviour?
- **"At least one skill" is a suspicious requirement.** A skill that exists to satisfy a checklist is a token cost with no payoff. Is the floor *having* a skill, or is it something upstream of that — the repo being legible enough that skills are worth writing?
- **Each item needs a stated failure mode.** If a repo lacks it, what specifically goes wrong for the agent? An item with no crisp answer is house style wearing floor clothing.
- **Distinguish "present" from "correct".** The floor should be checkable without judging quality — otherwise the audit needs taste, which does not scale and cannot be self-served.

Resolution is the capability list, each with its failure mode, and an explicit note of what was considered and rejected to house style.

## Answer

**The floor is six capabilities. All are required — none was demoted to house style.**

Ranked by what fails first in a repo with no config at all:

| # | Capability | What breaks without it | Shared between the two agents? |
|---|---|---|---|
| 1 | **How to build, test and run** | The agent guesses commands, runs the wrong ones, and rediscovers them every session. Named the single highest-payoff item. | n/a — it is content, not a format |
| 2 | **What the project is and where code lives** | The agent edits plausible but wrong files and invents structure that does not exist. | n/a |
| 3 | **Coding standards and conventions** | Code works but does not match the codebase; every PR collects style comments. | n/a |
| 4 | **Skills** | No repeatable workflow; every task is improvised, no reuse. | **Yes** — one `SKILL.md` serves both |
| 5 | **Saved prompts** | Common tasks are rebuilt from scratch each time, with different results each time. | **No** — Copilot `.prompt.md` vs Claude Code slash commands |
| 6 | **Sub-agents / custom agents** | No specialisation; one general agent does every job and is mediocre at each. | **No** — different formats entirely |

### Consequences that follow

- **Item 1 is verifiable by execution.** Run the documented commands and see whether they work. No judgement needed. The other five need an opinion. So the check can be *deterministic for the most important item* and only fall back to an LLM judge above it — this feeds [What the LLM judge assesses, and against what rubric](06-how-the-floors-effect-gets-measured.md).
- **A long floor is affordable only because the vendors generate it.** The list is long, but Claude Code's `/init` and Copilot's `/init` write most of items 1–3 automatically. The floor costs a team roughly half an hour, not several days. If that stopped being true, this answer should be revisited — the size was accepted *on that basis*.
- **Two of six items are not portable, and that is new work nobody has scoped.** Saved prompts and sub-agents must exist in two formats and stay in sync. This is a maintenance burden the floor creates, not one it removes, and it makes the "one source, many outputs" problem real — see the extension to [Which files and paths satisfy the floor in both agents](04-which-files-and-paths-satisfy-the-floor.md). The prior-art research parked `Ruler` as "revisit if the floor needs a distribution mechanism"; it now does.

### What this ticket did NOT settle

**Present versus correct.** The floor is defined as capabilities that must exist. Whether "exists" is enough, or each item must also be *accurate*, is deliberately left to the judging rubric ticket. Items 4–6 can trivially be satisfied by an empty file that helps nobody, so at least those need a correctness bar.
