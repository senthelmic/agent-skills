# Map: Agent config floor

Label: `wayfinder:map`

## Destination

A spec, ready to hand to implementation, for a **phased enablement toolkit** that helps any team get their repo up and running quickly with the documentation, instructions and skills Claude Code and GitHub Copilot actually need — runnable self-serve or with platform-team assistance, never enforced.

**This is a help exercise, not an audit.** The primary job is getting a team from zero to working, fast. Measurement is a **deferred final phase**: valuable, planned for, and explicitly not load-bearing. Every earlier phase must be worth shipping on its own if the final phase never arrives.

The toolkit has **two goals**, and both are in scope from the start:

1. **Audit and recommend** — look at the repo, report what is missing against the floor, and recommend the skills this particular project should have based on its technology stack.
2. **Generate** — interview the developer and produce the missing artefacts. In an existing codebase the tool reads the code first and pre-fills each interview answer with a recommendation; in a new codebase it has only the interview.

Goal 1 must work without goal 2. A team that wants only the report must be able to take it and act alone.

Reaching the end of this map means: nothing is left to decide before someone builds it.

## Notes

**Domain.** Internal developer platform / agent enablement. Exactly two target agents: **Claude Code** and **GitHub Copilot**. No others.

**Ubiquitous language.**

- **Floor** — the minimum agent-config set every repo must have. A minimum, never a template.
- **Below the floor** — a repo missing any part of it.
- **House style** — whatever a team adds above the floor. Explicitly theirs; never generated, never standardised.
- **Self-serve** — a team meets the floor alone, with zero platform-team involvement and no visibility to us.
- **Assisted** — a team invites the platform team in to audit and improve with them.
- **Target** — which agents a run is for: Claude Code, Copilot, or both. Defaults to both.
- **Stack** — the technologies a repo actually uses, as detected from the code: language, framework, database, and notable libraries.
- **Catalogue** — the set of recommendable skills the tool can point a team at. Its source is an open question and the largest unscoped risk on this map.
- **Greenfield** — a new or near-empty codebase. Nothing to read, so recommendations come from the interview alone.
- **Brownfield** — an existing codebase. The tool reads it first and pre-fills each interview answer with a recommendation.
- **Phase** — a shippable increment that delivers value without any later phase existing.
- **Final phase** — the deferred measurement-and-audit increment. Referred to as "the final phase" throughout; never assumed present by anything earlier.

**Standing constraints** (settled during charting — do not relitigate without saying so):

1. **Invitational, not enforcement.** No required CI checks, no blocking merges, no unconsented scanning. A team that wants nothing to do with the platform team must still have a complete path to the floor.
2. **Not standardisation.** Teams are at deliberately different maturity levels and diverge upward freely. The goal is raising the worst, not converging the rest.
3. **Skills are already portable — don't rebuild it.** Copilot reads `.claude/skills/` natively, same `SKILL.md` format and frontmatter, resolving from `.github/skills`, `.claude/skills`, or `.agents/skills`. One skills directory serves both agents with zero translation.
4. **Instructions are not portable — this is the real gap.** Reach is uneven. `.github/copilot-instructions.md` is the **only** path that reaches every Copilot surface. `AGENTS.md` reaches every cloud agent, VS Code Chat, Copilot CLI, and code review **only on github.com**. `CLAUDE.md` reaches every cloud agent surface and Copilot CLI, but no Chat outside cloud agent and no code review. So a team with everything in `CLAUDE.md` is invisible to Copilot Chat in Visual Studio, JetBrains, Xcode and github.com, and to code review everywhere — a silent failure, because the config looks present. _(Cells corrected by the prior-art research, which found the original wording overstated the gap in one direction and understated it in another. The asymmetry conclusion is unchanged and sharper.)_
5. **Effectiveness is judged before it is measured.** In every phase but the last, the quality bar is qualitative — complete, correct, in paths the agents actually read — with an LLM as the judge. No telemetry, no instrumentation. Quantitative measurement of token spend and response quality is the **final phase only**.
6. **Every phase stands alone.** No phase may depend on a later one to deliver value, and nothing before the final phase may depend on measurement existing. If the final phase is never built, everything shipped before it still works and was still worth shipping. This is a hard architectural constraint, not a sequencing preference — it decides what data the early phases have to capture, and whether they capture any at all.
7. **Help, not audit.** When a ticket's answer could go either way, favour the reading that gets a struggling team running sooner over the one that assesses them more rigorously.
8. **The team chooses the agents.** Every run takes a target: Claude Code, Copilot, or both. **Default is both.** This is not cosmetic — it decides how much duplicated work the floor demands, since saved prompts and sub-agents must be authored once per agent. A team targeting one agent has a materially smaller floor than a team targeting both, and the default is the expensive one.
9. **Recommendations are stack-aware.** The floor's "skills" item is not one generic skill. The tool inspects the project and recommends the skills that project needs — front-end, back-end, database for a full-stack app; a React or Angular skill for the framework; and specific skills for libraries with steep learning curves or easily-misused patterns (TanStack Query, Apollo GraphQL, React Hook Form). What is recommended for what is settled during phasing.
10. **Converge on technology, diverge on team.** Recommending the same React skill to every React team *is* convergence — but on the technology, not on how a team works. That is compatible with "not standardisation", which forbids imposing one team's working style on another. Keep the distinction: a skill about a library is shareable; a skill about how *we* review code is house style.

**Skills every session should consult:** `/grilling` and `/domain-modeling` by default; `/research` for AFK tickets; `/prototype` where the question is "what should this look like".

**Mode:** planning. Produce decisions, not deliverables.

## Decisions so far

<!-- one line per closed ticket -->

- [Build, fork, or wrap the existing audit tools](issues/03-build-fork-or-wrap-existing-tools.md) — **Build fresh, adopt nothing.** Every existing tool answers "how good is this file?", but the floor asks "does this repo have anything at all?" — a different question a zero-config repo cannot even produce output for. None of them knows Copilot's instruction layer, so all of them miss the actual gap. Wrap the **vendors' own** `/init` commands for generating content; never write our own generator. Full findings: [research/prior-art.md](research/prior-art.md).
- [What capabilities make up the floor](issues/01-what-capabilities-make-up-the-floor.md) — **Six capabilities, all required, nothing demoted to house style:** build/test/run, what the project is and where code lives, coding standards, skills, saved prompts, sub-agents. Ranked by what fails first. Affordable only because the vendors' `/init` generates most of items 1–3. Two of the six (saved prompts, sub-agents) are **not portable** between the agents and must exist twice.

## Not yet specified

In scope, not yet sharp enough to ticket:

- **The shape of the assisted offer.** How a team invites the platform team in, what the engagement actually consists of, and how it ends. Hangs on knowing what the floor is before the service around it can be described.
- **Ownership and evolution of the floor.** Who decides the floor changed, how repos already above it learn they've drifted below a raised bar, and whether meeting the floor expires.
- **Repos that are already good.** Whether a repo above the floor gets migrated, certified, left alone, or mined as the source of the floor's own content.
- **Rollout and pilot selection.** Which teams go first, and whether the willing-but-weak or the strong-and-skeptical make the better first cohort.
- **Whether the floor reaches past the six settled capabilities** — MCP servers and hooks specifically. Sub-agents and saved prompts are now *in* the floor; these two were not raised and remain undecided.
- **How an installed recommended skill stays current.** A team that copies a catalogue skill into their repo owns a fork of it. Whether they ever get improvements is a distribution question nobody has opened.
- **Cross-pollination of house style.** Whether good patterns from strong teams get shared, and whether that can happen without becoming the standardisation this effort rules out.

## Out of scope

- **Enforcement mechanisms** — required CI checks, blocking merges, org-wide policy gates. Ruled out by the invitational constraint; a floor that polices is a different product with a different mandate.
- **Agents other than Claude Code and GitHub Copilot** — Cursor, Codex, Gemini and the rest. Named out explicitly at the outset.
- **Standardising house style** — converging teams on identical config above the floor. Directly contrary to the destination.

<!-- Telemetry and quantitative measurement were briefly ruled out here, then returned when the destination was redrawn as phased. They are in scope, deferred to the final phase. See "Every phase stands alone" in Notes. -->

- **Token consumption as an early-phase goal** — optimising for spend before the final phase. Good setup may reduce it; that is a side effect, and it must not shape earlier decisions.
