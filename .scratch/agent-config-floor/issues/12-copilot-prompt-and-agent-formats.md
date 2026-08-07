# What Copilot's saved-prompt and custom-agent formats actually are

Type: research
Status: open
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
