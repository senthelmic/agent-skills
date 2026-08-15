# What Copilot's saved-prompt and custom-agent formats actually are

Type: research
Status: open — phase-one half answered 2026-08-14; translator loss analysis still open
Blocked by: 04

## Question

[Which files and paths satisfy the floor in both agents](04-which-files-and-paths-satisfy-the-floor.md) decided that saved prompts and sub-agents are authored Claude-native and **converted** into Copilot's formats. That conversion is a translator, not a copy, and it cannot be designed without knowing precisely what it is translating into.

The Claude Code side is settled and needs no research: `.claude/commands/*.md` for slash commands, `.claude/agents/*.md` for sub-agents.

Establish, with citations to primary GitHub and VS Code documentation:

- **Saved prompts.** Confirm `.github/prompts/*.prompt.md` is the current path. Report the full frontmatter schema (`mode`, `model`, `tools`, `description`, anything else), the placeholder syntax, how a prompt is invoked on each surface, and **which Copilot surfaces read prompt files at all** — the instruction-file matrix does not cover them, and reach may be much narrower.
- **Custom agents — the actual open question.** The path was not verified while resolving ticket 04 and must not be guessed. Determine whether it is `.github/agents/*.agent.md`, the older chat-modes path `.github/chatmodes/*.chatmode.md`, both, or something else; whether one supersedes the other; and again which surfaces read them.
- **What a Claude-to-Copilot conversion loses.** Compare the two formats field by field and name what has no equivalent on the Copilot side. Known differences to start from: placeholders (`$ARGUMENTS`, `$1`, `$2` against `${input:name}`), tool restrictions, model pinning, and Claude Code's `!` bash execution and `@` file references. Anything with no counterpart is silent breakage in the generated file.
- **Whether the loss is severe enough to reopen ticket 04.** That ticket accepted lossy conversion as a known risk rather than solving it. If the translator turns out to drop something load-bearing, say so plainly — the fallback options recorded there are two hand-maintained copies, or satisfying these capabilities through skills instead.

Resolution is the two path-and-schema answers, the surface-reach lists, and an explicit verdict on whether lossy conversion remains acceptable.

## Priority raised — this now gates phase one

[How the effort is phased, and what each phase stands up alone](08-how-the-effort-is-phased.md) put **all six capabilities into the phase-one audit**, not just the ones phase two generates. The Copilot side of capabilities 5–6 cannot be audited without knowing the paths, so this research blocks the *first* shippable phase rather than the fourth.

The audit needs less than the translator does. Separate the two in the answer, so phase one is not held up by translator detail:

- **Needed for phase one:** the exact paths, and the frontmatter that makes a file valid. Nothing more — phase one checks presence plus well-formedness.
- **Needed for phase four:** the full field-by-field comparison and the loss analysis.

## Answer — the phase-one half only

Researched 2026-08-14. Full working and every citation:
[../research/copilot-formats.md](../research/copilot-formats.md).

**Custom agents: `.github/agents/<name>.agent.md`.** Confirmed three ways —
GitHub's cloud-agent template ("`my-agent.agent.md` in the `.github/agents`
directory"), the Copilot CLI documentation, and `github/awesome-copilot`, which
holds 226 `*.agent.md` files and zero `*.chatmode.md`. Only `description` is
required in the frontmatter. `.chatmode.md` is the **superseded name for the
same thing**, not a parallel path: accept it when auditing, never generate it.

**Saved prompts: `.github/prompts/<name>.prompt.md` — path confirmed, reach far
narrower than assumed.** Read by VS Code, Visual Studio and JetBrains only, and
documented as public preview. Not github.com, not the cloud agent, not code
review, not Copilot CLI. This ticket's suspicion that "reach may be much
narrower" was correct.

**Two findings outside this ticket's scope that the research turned up anyway,
both affecting shipped behaviour:**

1. GitHub restructured the instruction matrix. `CLAUDE.md`, `AGENTS.md` and
   `GEMINI.md` are now **one type with identical reach**, which weakens the
   asymmetry in map constraint 4 without overturning its conclusion —
   `CLAUDE.md` alone still leaves nine of eighteen surfaces blind.
2. A fifth type, `.github/instructions/**/*.instructions.md`, is read by
   thirteen of the eighteen surfaces and was **absent from the shipped reach
   matrix entirely**. A team using it was being told, falsely, that its agents
   see nothing.

**Verdict on the ticket's own question:** lossy conversion remains acceptable
for phase 1. The paths and required frontmatter are now known, which is all
phase 1 needs.

**What stays open.** The field-by-field loss analysis, and whether the loss
reopens ticket 04. Status stays `open` for that reason.

**One thing for phase 2 to decide, surfaced by this research.** For a
Copilot-targeting team, a saved prompt buys three preview-status editor chat
windows while a custom agent buys the cloud agent, code review and the CLI. If
the floor ever has to demand only one of capabilities 5 and 6, agents are the
better buy. Not decided here.
