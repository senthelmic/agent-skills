# Which files and paths satisfy the floor in both agents

Type: grilling
Status: resolved
Blocked by: 01

## Question

Once the floor's capabilities are settled, decide the concrete files and paths that satisfy each one across **both** Claude Code and GitHub Copilot — and how a check verifies them.

The constraint that makes this non-trivial is the instruction-file support matrix (see the map's standing constraints): `.github/copilot-instructions.md` reaches every Copilot surface, `AGENTS.md` reaches cloud agent plus VS Code chat plus code review, and `CLAUDE.md` reaches **Copilot cloud agent only**. Skills, by contrast, are already portable via `.claude/skills/`.

Decide:

- **Which file is the source of truth for instructions**, and how the others stay in sync — generated, symlinked, cross-referenced with a pointer, or duplicated and drift-checked. Each has a different failure mode; pick one and say why.
- **Whether the floor mandates a specific layout or accepts any layout that demonstrably loads.** Mandating is checkable but prescriptive; accepting-if-it-loads respects house style but is harder to verify.
- **How the check proves an agent actually reads the file**, rather than that a file exists at a path. Presence is trivially checkable and nearly worthless — a correctly named file in a location the tool ignores is precisely the invisible failure this effort exists to catch.
- **Whether `.github/skills`, `.claude/skills`, or `.agents/skills` is the floor's canonical skills location**, given all three resolve for Copilot but Claude Code has its own expectation.

**Added after the floor was settled — the duplication problem.** The floor's six capabilities split three ways, and only one way is free:

- **Instructions** (items 1–3) — one logical thing, several files with different reach. The asymmetry above.
- **Skills** (item 4) — genuinely portable. One `SKILL.md` serves both agents. Nothing to solve.
- **Saved prompts and sub-agents** (items 5–6) — **not portable at all.** Copilot `.prompt.md` and custom agents versus Claude Code slash commands and sub-agents: different formats, different directories. Every one of these must be authored twice and kept in sync forever, and the floor is what creates that burden.

So decide additionally:

- **Does one source generate both formats, or does a team maintain two copies by hand?** Hand-maintained copies drift, and drift is invisible until an agent behaves differently in one tool. Generation needs a source format that is nobody's native format, which is its own cost.
- **Is `Ruler` the answer here?** The prior-art research parked it as "revisit if the floor needs a distribution mechanism" — it now does. Re-read that section before deciding. Note its two disqualifiers found there: it maps Copilot to `AGENTS.md` rather than `.github/copilot-instructions.md`, and it gitignores generated config, which would hide the files from the Copilot surfaces that read the committed repo.
- **Or does the floor accept either format and not require both?** Cheapest option, and it means a repo can be at the floor for Claude Code but not Copilot. Decide whether that is acceptable or defeats the purpose.

Resolution is a path-level definition of the floor, plus the verification method for each item, plus the sync strategy for the two non-portable items.

## Answer

**One file is authored, the rest are generated from it and committed. Which file is authored depends on the target. `AGENTS.md` leaves the floor entirely.**

### `AGENTS.md` is dominated — drop it

Every Copilot surface `AGENTS.md` reaches, `.github/copilot-instructions.md` also reaches, and Claude Code does not read `AGENTS.md` at all. It buys nothing the other two files do not already buy. This is forced by GitHub's published matrix, not a preference.

A repo that already has an `AGENTS.md` is not penalised — the audit counts it as reaching what the matrix says it reaches. The floor simply never asks anyone to create one.

### Path-level definition of the floor

Instructions, by target:

| Target | Authored by hand | Generated and committed |
|---|---|---|
| Both | `CLAUDE.md` | `.github/copilot-instructions.md` |
| Copilot only | `.github/copilot-instructions.md` | — |
| Claude only | `CLAUDE.md` | — |

Skills, by target — one directory, no duplication, because `SKILL.md` is already portable:

| Target | Path |
|---|---|
| Both, or Claude only | `.claude/skills/` |
| Copilot only | `.github/skills/` |

`.claude/skills/` is the only path both agents read: Claude Code reads that and nothing else, Copilot resolves all three candidates. So the both-target case is forced. Copilot-only gets `.github/skills/` so that a repo with no Claude in it does not carry a `.claude/` directory. If the target later changes, the toolkit moves the directory.

Saved prompts and sub-agents, by target:

| Target | Authored by hand | Generated and committed |
|---|---|---|
| Both | `.claude/commands/*.md`, `.claude/agents/*.md` | Copilot's prompt and custom-agent paths |
| Copilot only | Copilot's paths | — |
| Claude only | `.claude/commands/`, `.claude/agents/` | — |

The exact Copilot paths are **not settled here** — see "What this ticket did NOT settle".

### Why `CLAUDE.md` is the authored side when the target is both

Not because Claude Code matters more. Three practical reasons:

- **`CLAUDE.md` supports `@imports`; `.github/copilot-instructions.md` has no import mechanism.** The modular file is therefore the natural source and the flat file the natural output. Generating in the other direction would mean discarding structure and never recovering it.
- **Claude Code's `/init` already reads `.github/copilot-instructions.md`** and folds it in. A Copilot-only repo that later adds Claude does not start from nothing — the vendor tool bootstraps the new source file from the existing one, and generation runs forward from then on.
- **Copying is distribution, not content generation.** This does not break the "wrap the vendors' `/init`, never write our own generator" rule set by [Build, fork, or wrap the existing audit tools](03-build-fork-or-wrap-existing-tools.md). The vendors still write the content; we only move it.

**Generated content must be flattened, not copied byte-for-byte.** Any `@import` in the authored `CLAUDE.md` must be resolved inline when writing the Copilot file, because Copilot will not follow the reference and would silently receive less than the author intended.

### Mandate to generate, accept-if-loads to audit

The two goals get different rules, and this is deliberate:

- **Generate** writes the paths above. A generator that asks the team to invent a layout has failed at its job.
- **Audit** passes any layout it can show reaches the agents. It reports below-the-floor only when a surface genuinely sees nothing.

The reason for the split: an audit that fails a well-configured team with an unusual layout is the fastest way to make that team stop running the tool. Constraint 2 (not standardisation) and constraint 7 (help, not audit) both point the same way.

### Verification method

**Instructions — computed reach, not presence.** The check combines the files that exist with GitHub's published support matrix, held as versioned data inside the toolkit, and reports which of the 18 Copilot surfaces currently see instructions and which see nothing. This is what answers the ticket's objection that presence is nearly worthless: a repo holding only `AGENTS.md` is reported as "Visual Studio Chat sees nothing", not as "a file exists". The prior-art research found no existing tool encodes this matrix.

The method is deterministic, needs nothing installed, and gives the same answer twice — so the identical code runs in self-serve, in assisted mode, and later in the final phase.

**Plus a self-test line.** Generated instruction files carry one marker line — if asked whether project instructions are loaded, reply with a known token. A developer can then confirm loading in whichever surface they personally use, in about ten seconds, with no platform-team involvement. Cost is a few tokens in every context that loads the file. This exists because the matrix is only as good as GitHub's documentation, and a team needs a way to check reality themselves.

**Skills, prompts and sub-agents — presence at the mandated path, plus valid frontmatter.** There is no reach matrix for these: each agent reads one location, so presence at that location is reach. Whether the content is any *good* is not this ticket's question.

**Build, test and run — by execution**, as already settled in [What capabilities make up the floor](01-what-capabilities-make-up-the-floor.md).

### Sync strategy for the two non-portable items

Authored Claude-native, converted out. Same shape as the instruction files, with one honest difference: **this is a translator, not a copy.** Placeholders differ (`$ARGUMENTS` against `${input:name}`), frontmatter keys differ, and invocation differs. A lossy conversion produces a prompt that works in one agent and quietly misbehaves in the other.

That risk is **accepted, not solved.** It is the price of the floor demanding these two capabilities at all. What the conversion actually loses is the subject of the research ticket below, and if the loss turns out to be severe, this decision should be revisited rather than worked around.

Rejected alternatives:

- **A neutral source format** would force every team to learn a format no vendor documents, no vendor tooling generates, and no vendor will keep working when they change theirs.
- **Two hand-maintained copies** make drift invisible until an agent behaves differently in one tool, and leave the floor demanding work it offers no help with.
- **Satisfying both through skills** was considered seriously, since skills are already portable and this is the only option that makes the duplication disappear rather than managing it. Rejected because it partly undoes [What capabilities make up the floor](01-what-capabilities-make-up-the-floor.md): those capabilities were separated because they fail differently — a skill is invoked by the agent on its own judgement, a saved prompt is invoked deliberately by a person, and a sub-agent gets its own separate context.

### Ruler is not adopted

Three reasons, each sufficient alone:

1. **Its source model is a neutral `.ruler/` directory** — the option rejected above.
2. **Its default Copilot output is `AGENTS.md`**, the one file this ticket removes from the floor. Configurable, but the default tells you what the project is designed around.
3. **It does not touch saved prompts or sub-agents**, which is where the actual duplication burden sits. It would solve the easy third of the problem.

The unverified gitignore behaviour flagged by the research — generated files hidden from the Copilot surfaces that read the committed repository — is a fourth reason, but the decision does not rest on it, so it needs no further investigation.

**Watching brief retained.** If Ruler grows saved-prompt and sub-agent support, reopen this.

### Non-destructive by construction

Generated files are committed and look like ordinary files, so a developer will eventually edit one directly.

- Every generated file opens with a header saying it is generated and naming the file to edit instead.
- The toolkit records a checksum, so it can distinguish an untouched copy from an edited one.
- **Untouched** regenerates silently. **Edited** stops the run and reports the conflict, leaving the choice to the developer.

The toolkit never destroys work, and never needs a human present for the common case. Always-overwrite-with-backup was rejected as too close to the Ruler behaviour the research flagged as disqualifying; always-ask was rejected because it makes the toolkit unrunnable unattended.

### What this ticket did NOT settle

- **Correct versus present.** An empty `SKILL.md` at the right path passes every check here. [What capabilities make up the floor](01-what-capabilities-make-up-the-floor.md) already routed the correctness bar to [What the LLM judge assesses, and against what rubric](06-how-the-floors-effect-gets-measured.md), and this ticket keeps it there.
- **The exact Copilot saved-prompt and custom-agent paths and formats.** Claude Code's side is settled (`.claude/commands/*.md`, `.claude/agents/*.md`). Copilot's prompt files are `.github/prompts/*.prompt.md`; the custom-agent path was **not** verified and is not guessed here. Spun off as [What Copilot's saved-prompt and custom-agent formats actually are](12-copilot-prompt-and-agent-formats.md), which the translator design depends on.
