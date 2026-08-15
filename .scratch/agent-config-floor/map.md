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
- **Catalogue** — _retired 2026-08-07._ There is none: nothing is authored, everything is generated per repo. Kept in the language only so older tickets remain readable.
- **Stub** — a one-line placeholder file the survey run writes at an artefact's correct path, carrying a `TODO(floor):` marker. Visible structure for a beginner; **never counts as present**.
- **Greenfield** — a new or near-empty codebase. Nothing to read, so recommendations come from the interview alone.
- **Brownfield** — an existing codebase. The tool reads it first and pre-fills each interview answer with a recommendation.
- **Phase** — a shippable increment that delivers value without any later phase existing.
- **Final phase** — the deferred measurement-and-audit increment. Referred to as "the final phase" throughout; never assumed present by anything earlier.

**Standing constraints** (settled during charting — do not relitigate without saying so):

1. **Invitational, not enforcement.** No required CI checks, no blocking merges, no unconsented scanning. A team that wants nothing to do with the platform team must still have a complete path to the floor.
2. **Not standardisation.** Teams are at deliberately different maturity levels and diverge upward freely. The goal is raising the worst, not converging the rest.
3. **Skills are already portable — don't rebuild it.** Copilot reads `.claude/skills/` natively, same `SKILL.md` format and frontmatter, resolving from `.github/skills`, `.claude/skills`, or `.agents/skills`. One skills directory serves both agents with zero translation.
4. **Instructions are not portable — this is the real gap.** _(Corrected again on 2026-08-14 against GitHub's live documentation — see [research/copilot-formats.md](research/copilot-formats.md). GitHub now groups `AGENTS.md`, `CLAUDE.md` and `GEMINI.md` as one "agent instructions" type with **identical** reach, so the `AGENTS.md` versus `CLAUDE.md` distinction below no longer holds. A fifth type, `.github/instructions/**/*.instructions.md`, reaches thirteen of eighteen surfaces and was missing from this constraint entirely. The asymmetry conclusion survives: `CLAUDE.md` alone still leaves nine of eighteen surfaces blind, and `.github/copilot-instructions.md` is still the only universal path. Read the research file, not the wording below.)_ Reach is uneven. `.github/copilot-instructions.md` is the **only** path that reaches every Copilot surface. `AGENTS.md` reaches every cloud agent, VS Code Chat, Copilot CLI, and code review **only on github.com**. `CLAUDE.md` reaches every cloud agent surface and Copilot CLI, but no Chat outside cloud agent and no code review. So a team with everything in `CLAUDE.md` is invisible to Copilot Chat in Visual Studio, JetBrains, Xcode and github.com, and to code review everywhere — a silent failure, because the config looks present. _(Cells corrected by the prior-art research, which found the original wording overstated the gap in one direction and understated it in another. The asymmetry conclusion is unchanged and sharper.)_
5. **Effectiveness is judged before it is measured.** In every phase but the last, the quality bar is qualitative — complete, correct, in paths the agents actually read — with an LLM as the judge. No telemetry, no instrumentation. Quantitative measurement of token spend and response quality is the **final phase only**.
6. **Every phase stands alone.** No phase may depend on a later one to deliver value, and nothing before the final phase may depend on measurement existing. If the final phase is never built, everything shipped before it still works and was still worth shipping. This is a hard architectural constraint, not a sequencing preference — it decides what data the early phases have to capture, and whether they capture any at all.
7. **Help, not audit.** When a ticket's answer could go either way, favour the reading that gets a struggling team running sooner over the one that assesses them more rigorously.
8. **The team chooses the agents.** Every run takes a target: Claude Code, Copilot, or both. **Default is both.** This is not cosmetic — it decides how much duplicated work the floor demands, since saved prompts and sub-agents must be authored once per agent. A team targeting one agent has a materially smaller floor than a team targeting both, and the default is the expensive one.
9. **Recommendations are stack-aware.** The floor's "skills" item is not one generic skill. The tool inspects the project and recommends the skills that project needs — front-end, back-end, database for a full-stack app; a React or Angular skill for the framework; and specific skills for libraries with steep learning curves or easily-misused patterns (TanStack Query, Apollo GraphQL, React Hook Form). What is recommended for what is settled during phasing.
10. **Converge on technology, diverge on team.** Recommending the same React skill to every React team *is* convergence — but on the technology, not on how a team works. That is compatible with "not standardisation", which forbids imposing one team's working style on another. Keep the distinction: a skill about a library is shareable; a skill about how *we* review code is house style.

**Skills every session should consult:** `/grilling` and `/domain-modeling` by default; `/research` for AFK tickets; `/prototype` where the question is "what should this look like".

**Mode:** planning through 2026-08-06; the spec is now written and the effort moves to implementation.

**Constraint 5 was amended on 2026-08-06.** "The audit is deterministic and needs no agent" no longer holds — the user directed that an LLM do both the audit and the generation, with near-determinism bought by a fixed reading budget, lookup-shaped checks, a fixed output schema and a strict system prompt. Constraints 1, 2, 6, 7, 8, 9 and 10 are unaffected.

## Decisions so far

<!-- one line per closed ticket -->

- [Build, fork, or wrap the existing audit tools](issues/03-build-fork-or-wrap-existing-tools.md) — **Build fresh, adopt nothing.** Every existing tool answers "how good is this file?", but the floor asks "does this repo have anything at all?" — a different question a zero-config repo cannot even produce output for. None of them knows Copilot's instruction layer, so all of them miss the actual gap. Wrap the **vendors' own** `/init` commands for generating content; never write our own generator. Full findings: [research/prior-art.md](research/prior-art.md).
- [What capabilities make up the floor](issues/01-what-capabilities-make-up-the-floor.md) — **Six capabilities, all required, nothing demoted to house style:** build/test/run, what the project is and where code lives, coding standards, skills, saved prompts, sub-agents. Ranked by what fails first. Affordable only because the vendors' `/init` generates most of items 1–3. Two of the six (saved prompts, sub-agents) are **not portable** between the agents and must exist twice. **A code-review skill or prompt is mandatory in every repo by name**, whatever the stack — see the ticket's "Named mandatory artefacts" section.
- [Which files and paths satisfy the floor in both agents](issues/04-which-files-and-paths-satisfy-the-floor.md) — **One file is authored, the rest are generated from it and committed.** Which one is authored depends on the target; `AGENTS.md` **leaves the floor** as dominated. Verification is **computed reach** from GitHub's published matrix — held as versioned data the toolkit owns — never bare presence. Paths are mandated to _generate_, accept-any-layout-that-reaches to _audit_. Prompts and sub-agents are translated Claude-native → Copilot, lossy conversion accepted as a known risk. **Ruler not adopted**; watching brief retained.

- [How a team runs the floor tool with zero platform involvement](issues/05-how-a-team-reaches-the-floor-unassisted.md) — **Two mechanisms behind one front door, split on determinism.** `npx @org/floor` is a deterministic CLI for the audit; `--setup` delivers an **ephemeral** skill for the judgement half, which routes the developer to their own vendor's generator and is removed after the run. Always-latest, no pinning — the floor's version travels with the tool, so nobody has to be told it moved. **Never phones home**; the report is a dated, version-stamped file, which is the voluntary bridge into the assisted path. On adoption the answer is honestly **"nothing"** — the mechanism only removes reasons not to run it. Two rules established: every file the toolkit leaves behind is the team's and counts toward the floor; the final phase cannot backfill history.

- [How the effort is phased, and what each phase stands up alone](issues/08-how-the-effort-is-phased.md) — **Five phases, ordered by value per unit of risk:** audit · generate instructions · recommend skills · prompts and sub-agents · measurement. **Phase one is audit-only** but covers all six capabilities, stays deterministic (documented-presence only; `--verify` executes on opt-in and never counts toward the verdict), and needs no agent installed. **The catalogue is not a phase** — it is an optional content stream, and no phase may depend on it. The **LLM judge is phase 2**, where an agent already runs and the rubric doubles as the generator's target. **The data question:** the report file is the record — stable path, machine-readable header, the team commits it, history is patchy by design. Every dependency arrow points backwards.

- [What the LLM judge assesses, and against what rubric](issues/06-how-the-floors-effect-gets-measured.md) — **There is no judge; there is a gap-finder that then helps.** Reads in two bounded passes — claim-check (each claim in the config becomes one lookup, so cost scales with what the config *says*, not repo size) and a fixed-budget omission sweep. Returns an ordered gap list with evidence and next action — **never a score**. Rubric is one file, `floor.md`, read in both directions so it cannot drift: present · well-formed · specific · true. Near-determinism by construction, judgement calls disclosed, verdict stated as advisory. Self-grading is deliberate; the loop is broken by re-running the claim check after generating and writing unverifiable claims as `TODO(floor):` questions.
- [How the tool detects the stack](issues/10-how-the-stack-is-detected.md) — **Manifests are the source of truth, read within the same fixed budget.** Presence is the trigger and every recommendation carries its evidence; the developer judges significance, because measuring real usage needs reading the budget forbids. **Monorepo = more than one manifest → per-package recommendations, per-repo floor.** Library significance stays catalogue metadata, never detector logic, which is what keeps the catalogue off every phase's critical path.
- [What the floor report looks like to a team below it](issues/07-what-the-floor-report-looks-like.md) — **Framing: "what your agents cannot do here yet."** Capability sentences, not missing-file lines; no score, no level, no percentage. Tested against the reader who decides it — a team lead who never asked for the file. Machine-readable YAML header stays in the same file as the prose (a split would drift), uses `absent` not `fail`. Names the fix, performs it only on acceptance. A repo above the floor gets three lines.
- [How the interview works, in a new repo and an existing one](issues/11-how-the-interview-works.md) — **One interview, hard cap of six questions asked.** Every pre-filled answer carries its evidence, never a bare default and never a confidence score. A half-finished run **always writes what it has**, with `TODO(floor):` lines for the rest, so abandonment never leaves the repo worse. Greenfield generates less on purpose. The assumed reader is the new joiner, and "I don't know" is an accepted answer.

- [Where the recommended skills come from](issues/09-where-recommended-skills-come-from.md) — **The catalogue is taken to zero. Nothing is authored, everything is generated per repo.** All four options rejected: each was a permanent staffing commitment, and the strongest case for authoring — that *library* knowledge is not in the repo — fails because it is in the model's weights already. The survey run writes a **marked stub** at the correct path for every artefact the repo should have, and `TODO(floor):` **means absent** so a stub can never produce a false pass. Then **one generator per artefact**, worked one at a time, each asking questions **only where the code is ambiguous**. Budget becomes per run, scoped by the artefact. Uncovered stacks stop being a problem — a COBOL team and a React team get identical treatment, because there is no mainstream encoded anywhere. Reopens one entry at a time only if a generated skill is observably bad.

- [What Copilot's saved-prompt and custom-agent formats actually are](issues/12-copilot-prompt-and-agent-formats.md#answer--the-phase-one-half-only) — **phase-one half answered, 2026-08-14.** Custom agents are `.github/agents/<name>.agent.md` (the shipped skill had guessed `<name>.md`); `.chatmode.md` is the superseded name for the same thing. Saved prompts are `.github/prompts/<name>.prompt.md` but reach **only VS Code, Visual Studio and JetBrains**, in public preview. Two corrections outside the ticket's scope: the instruction matrix is now type-based with `CLAUDE.md` equal to `AGENTS.md`, and `.github/instructions/**/*.instructions.md` was missing from the toolkit entirely. Full working: [research/copilot-formats.md](research/copilot-formats.md).

**Amended after the fact** (the user directed that phase 1 stop being a deterministic non-agent check):

- [How a team runs the floor tool with zero platform involvement](issues/05-how-a-team-reaches-the-floor-unassisted.md#amendment-the-cli-is-withdrawn-delivery-is-agent-native) — **`npx @org/floor` withdrawn.** Delivery is one skill run by the agent the team already has; install is "fetch this URL and follow it". Always-latest, ephemeral, never phones home — all unchanged. Self-generation becomes an accepted fallback where no vendor generator reaches the surface.
- [How the effort is phased](issues/08-how-the-effort-is-phased.md#amendment-phase-1-is-no-longer-deterministic-and-phases-1-and-2-merge) — **Phases 1 and 2 merge into "Bootstrap".** Three phases now: bootstrap · sharpen · measurement.
- [How the CLI reaches teams without Node](issues/14-how-the-cli-reaches-non-node-teams.md) — **closed as moot.** No Node dependency remains to reach around.
- [How the interview works](issues/11-how-the-interview-works.md#amendment-one-interview-per-artefact-questions-driven-by-discrepancies) and [What the LLM judge assesses](issues/06-how-the-floors-effect-gets-measured.md#amendment-todofloor-means-absent-and-the-budget-is-per-run) — amended by the catalogue decision: one interview **per artefact** driven by code ambiguity rather than a fixed list, budget per run, and `TODO(floor):` counts as absent.

## Destination reached

[spec.md](spec.md) — phase 1 (Bootstrap) is specified and ready to implement. The remaining open tickets (09 catalogue, 12 and 13 research, 02 telemetry) are deliberately off the critical path and are listed there.

## Not yet specified

In scope, not yet sharp enough to ticket:

- **The shape of the assisted offer.** How a team invites the platform team in, what the engagement actually consists of, and how it ends. Hangs on knowing what the floor is before the service around it can be described. **Now has a concrete entry point:** the self-serve run writes its report to a file, and a team sharing that file is the invitation. What happens next is unowned.
- **Getting the tool into the org's new-repo template.** Claimed as the one adoption lever in the unassisted-path decision, but who owns the template and how a template change propagates is not investigated.
- **Ownership and evolution of the floor.** Who decides the floor changed, how repos already above it learn they've drifted below a raised bar, and whether meeting the floor expires. **Now has a concrete first instance:** the files-and-paths decision makes GitHub's instruction-reach matrix _versioned data the toolkit owns_. When GitHub changes that matrix, every past verdict silently becomes wrong until someone updates it. Who watches for that, and how a stale matrix is detected, is unowned.
- **Repos that are already good.** Whether a repo above the floor gets migrated, certified, left alone, or mined as the source of the floor's own content.
- **Rollout and pilot selection.** Which teams go first, and whether the willing-but-weak or the strong-and-skeptical make the better first cohort.
- **Whether the floor reaches past the six settled capabilities** — MCP servers and hooks specifically. Sub-agents and saved prompts are now *in* the floor; these two were not raised and remain undecided.
- **Cross-pollination of house style.** Whether good patterns from strong teams get shared, and whether that can happen without becoming the standardisation this effort rules out.

## Out of scope

- **Enforcement mechanisms** — required CI checks, blocking merges, org-wide policy gates. Ruled out by the invitational constraint; a floor that polices is a different product with a different mandate.
- **Agents other than Claude Code and GitHub Copilot** — Cursor, Codex, Gemini and the rest. Named out explicitly at the outset.
- **Standardising house style** — converging teams on identical config above the floor. Directly contrary to the destination.

<!-- Telemetry and quantitative measurement were briefly ruled out here, then returned when the destination was redrawn as phased. They are in scope, deferred to the final phase. See "Every phase stands alone" in Notes. -->

- **Token consumption as an early-phase goal** — optimising for spend before the final phase. Good setup may reduce it; that is a side effect, and it must not shape earlier decisions.
