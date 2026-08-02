# Map: Agent config floor

Label: `wayfinder:map`

## Destination

A spec, ready to hand to implementation, for a **phased enablement toolkit** that helps any team get their repo up and running quickly with the documentation, instructions and skills Claude Code and GitHub Copilot actually need — runnable self-serve or with platform-team assistance, never enforced.

**This is a help exercise, not an audit.** The primary job is getting a team from zero to working, fast. Auditing and measurement are a **deferred final phase**: valuable, planned for, and explicitly not load-bearing. Every earlier phase must be worth shipping on its own if the final phase never arrives.

Reaching the end of this map means: nothing is left to decide before someone builds it.

## Notes

**Domain.** Internal developer platform / agent enablement. Exactly two target agents: **Claude Code** and **GitHub Copilot**. No others.

**Ubiquitous language.**

- **Floor** — the minimum agent-config set every repo must have. A minimum, never a template.
- **Below the floor** — a repo missing any part of it.
- **House style** — whatever a team adds above the floor. Explicitly theirs; never generated, never standardised.
- **Self-serve** — a team meets the floor alone, with zero platform-team involvement and no visibility to us.
- **Assisted** — a team invites the platform team in to audit and improve with them.
- **Phase** — a shippable increment that delivers value without any later phase existing.
- **Final phase** — the deferred measurement-and-audit increment. Referred to as "the final phase" throughout; never assumed present by anything earlier.

**Standing constraints** (settled during charting — do not relitigate without saying so):

1. **Invitational, not enforcement.** No required CI checks, no blocking merges, no unconsented scanning. A team that wants nothing to do with the platform team must still have a complete path to the floor.
2. **Not standardisation.** Teams are at deliberately different maturity levels and diverge upward freely. The goal is raising the worst, not converging the rest.
3. **Skills are already portable — don't rebuild it.** Copilot reads `.claude/skills/` natively, same `SKILL.md` format and frontmatter, resolving from `.github/skills`, `.claude/skills`, or `.agents/skills`. One skills directory serves both agents with zero translation.
4. **Instructions are not portable — this is the real gap.** Reach is lopsided: `.github/copilot-instructions.md` reaches every Copilot surface; `AGENTS.md` reaches cloud agent, VS Code chat, and code review; `CLAUDE.md` reaches **Copilot cloud agent only**. A team with everything in `CLAUDE.md` gets nothing in Copilot CLI, VS Code chat, or code review — an invisible failure, since the config looks present.
5. **Effectiveness is judged before it is measured.** In every phase but the last, the quality bar is qualitative — complete, correct, in paths the agents actually read — with an LLM as the judge. No telemetry, no instrumentation. Quantitative measurement of token spend and response quality is the **final phase only**.
6. **Every phase stands alone.** No phase may depend on a later one to deliver value, and nothing before the final phase may depend on measurement existing. If the final phase is never built, everything shipped before it still works and was still worth shipping. This is a hard architectural constraint, not a sequencing preference — it decides what data the early phases have to capture, and whether they capture any at all.
7. **Help, not audit.** When a ticket's answer could go either way, favour the reading that gets a struggling team running sooner over the one that assesses them more rigorously.

**Skills every session should consult:** `/grilling` and `/domain-modeling` by default; `/research` for AFK tickets; `/prototype` where the question is "what should this look like".

**Mode:** planning. Produce decisions, not deliverables.

## Decisions so far

<!-- one line per closed ticket -->

_(none yet — charting session only)_

## Not yet specified

In scope, not yet sharp enough to ticket:

- **The shape of the assisted offer.** How a team invites the platform team in, what the engagement actually consists of, and how it ends. Hangs on knowing what the floor is before the service around it can be described.
- **Ownership and evolution of the floor.** Who decides the floor changed, how repos already above it learn they've drifted below a raised bar, and whether meeting the floor expires.
- **Repos that are already good.** Whether a repo above the floor gets migrated, certified, left alone, or mined as the source of the floor's own content.
- **Rollout and pilot selection.** Which teams go first, and whether the willing-but-weak or the strong-and-skeptical make the better first cohort.
- **Whether the floor reaches past instructions and skills** — MCP servers, hooks, subagents, custom agents. Depends entirely on what "What capabilities make up the floor" settles.
- **Cross-pollination of house style.** Whether good patterns from strong teams get shared, and whether that can happen without becoming the standardisation this effort rules out.

## Out of scope

- **Enforcement mechanisms** — required CI checks, blocking merges, org-wide policy gates. Ruled out by the invitational constraint; a floor that polices is a different product with a different mandate.
- **Agents other than Claude Code and GitHub Copilot** — Cursor, Codex, Gemini and the rest. Named out explicitly at the outset.
- **Standardising house style** — converging teams on identical config above the floor. Directly contrary to the destination.

<!-- Telemetry and quantitative measurement were briefly ruled out here, then returned when the destination was redrawn as phased. They are in scope, deferred to the final phase. See "Every phase stands alone" in Notes. -->

- **Token consumption as an early-phase goal** — optimising for spend before the final phase. Good setup may reduce it; that is a side effect, and it must not shape earlier decisions.
