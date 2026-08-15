# Copilot formats and reach: verified against primary sources

Resolves the phase-1 half of
[12-copilot-prompt-and-agent-formats.md](../issues/12-copilot-prompt-and-agent-formats.md).
Researched: 2026-08-14. Every claim below traces to a source in
[Sources](#sources). Anything unverified is labelled.

Supersedes the instruction-reach table in
[prior-art.md](prior-art.md) (retrieved 2026-08-02), which GitHub has since
restructured.

---

## 1. Verdict, in one paragraph

The shipped skill was **right about the folders and wrong about one file
extension**, and it was **missing a whole instruction type**. The custom-agent
path was a guess that ticket 04 forbade guessing; it turns out to be nearly
right — right directory, missing suffix. The more serious finding is that
GitHub restructured the instruction matrix: `CLAUDE.md`, `AGENTS.md` and
`GEMINI.md` are now one type with identical reach, and a fifth type
(`.github/instructions/**/*.instructions.md`) exists that the skill never
checks. A team keeping instructions there is currently told, falsely, that
their agents cannot see anything.

---

## 2. Custom agents — the guessed path

**Correct path:** `.github/agents/<name>.agent.md`

The shipped skill said `.github/agents/<name>.md`. The directory is right. The
`.agent.md` suffix was missing.

Three independent confirmations:

1. GitHub Docs, creating custom agents for the cloud agent — "This will open a
   template agent profile called `my-agent.agent.md` in the `.github/agents`
   directory of your target repository."
2. GitHub Docs, custom agents for Copilot CLI — repository level
   `.github/agents/` with the `.agent.md` extension; user level
   `~/.copilot/agents/` with the same extension.
3. `github/awesome-copilot`, GitHub's own showcase repository — **226 files
   matching `*.agent.md`, zero files matching `*.chatmode.md`.** Counted via the
   git tree API on 2026-08-14.

**`.chatmode.md` is superseded, not parallel.** VS Code's documentation: "Custom
agents were previously known as custom chat modes. The functionality remains the
same, but the terminology has been updated." Existing files should be renamed to
`.agent.md`.

**A plain `.md` may also be accepted, but do not rely on it.** The custom-agents
configuration reference mentions "the configuration file's name (minus `.md` or
`.agent.md`)" when describing deduplication, which implies both are tolerated on
the GitHub side. No page recommends the bare form, and VS Code's rename
instruction points the other way. **Generate `.agent.md`. When auditing, accept
either.**

**Frontmatter.** Only `description` is required. Optional fields observed:
`name`, `argument-hint`, `tools`, `agents`, `model`, `user-invocable`,
`disable-model-invocation`, `target`, `mcp-servers`, `handoffs`, `hooks`
(preview). Note `argument-hint` and `handoffs` are documented as unsupported on
github.com — they are VS Code-side fields.

**Reach.** Cloud agent on github.com, Copilot CLI, VS Code, and — in public
preview — JetBrains, Eclipse and Xcode.

---

## 3. Saved prompts — path confirmed, reach much narrower than assumed

**Correct path:** `.github/prompts/<name>.prompt.md` — confirmed. This part of
ticket 04 was right.

**But prompt files reach only three surfaces, and they are in public preview.**
GitHub Docs: prompt files "are in public preview and subject to change, and are
only available in VS Code, Visual Studio, and JetBrains IDEs."

That excludes github.com Chat, the cloud agent, code review, Copilot CLI,
Eclipse and Xcode. Ticket 12 predicted exactly this — "the instruction-file
matrix does not cover them, and reach may be much narrower" — and it was right.

**Corroborating signal:** `github/awesome-copilot` contains **zero `.prompt.md`
files** and 226 `.agent.md` files. GitHub's own showcase no longer demonstrates
prompt files at all. That is a signal about direction, not a documented
deprecation, and is labelled as such.

**Frontmatter** (VS Code): all optional — `description`, `name`,
`argument-hint`, `agent`, `model`, `tools`. Placeholders are
`${input:name}` and `${input:name:placeholder}`, plus `${selection}`. Tools are
referenced in the body as `#tool:<name>`.

**This has a consequence the floor should consider, and it is a phase-2
decision, not settled here.** Capability 5 (saved prompts) currently costs a
team a second authored file whose Copilot half reaches three IDE chat windows
and may change without notice. Capability 6 (sub-agents) costs the same and its
Copilot half reaches the cloud agent, code review and the CLI. If the floor has
to demand one of the two for a Copilot-targeting team, agents are now clearly
the better buy. Flagged, not decided.

---

## 4. Instructions — the matrix has been restructured

GitHub no longer publishes a filename-by-surface grid. It publishes a
**type**-by-surface grid, with filenames defined per type.

**The five types and their filenames:**

| Type | Filename(s) |
|---|---|
| Repository-wide | `.github/copilot-instructions.md` |
| Path-specific | `.github/instructions/**/*.instructions.md` |
| Agent | `AGENTS.md`, `CLAUDE.md`, **or** `GEMINI.md` |
| Personal | UI settings; on CLI, `~/.copilot/copilot-instructions.md` or `~/.copilot/instructions/**/*.instructions.md` |
| Organization | GitHub organization settings, not a repository file |

**The full matrix, verbatim** (✓ = supported, — = not supported):

| Environment | Feature | Personal | Repository-wide | Path-specific | Agent | Organization |
|---|---|---|---|---|---|---|
| GitHub.com | Copilot Chat | ✓ | ✓ | — | — | ✓ |
| GitHub.com | Copilot cloud agent | — | ✓ | ✓ | ✓ | ✓ |
| GitHub.com | Copilot code review | — | ✓ | ✓ | ✓ | ✓ |
| VS Code | Copilot Chat | — | ✓ | ✓ | ✓ | — |
| VS Code | Copilot cloud agent | — | ✓ | ✓ | ✓ | — |
| VS Code | Copilot code review | — | ✓ | — | — | — |
| Visual Studio | Copilot Chat | — | ✓ | ✓ | — | — |
| Visual Studio | Copilot code review | — | ✓ | — | — | — |
| JetBrains | Copilot Chat | ✓ | ✓ | ✓ | — | — |
| JetBrains | Copilot cloud agent | — | ✓ | ✓ | ✓ | — |
| JetBrains | Copilot code review | — | ✓ | ✓ | — | — |
| Eclipse | Copilot Chat | — | ✓ | — | — | — |
| Eclipse | Copilot cloud agent | — | ✓ | ✓ | ✓ | — |
| Eclipse | Copilot code review | not supported | — | — | — | — |
| Xcode | Copilot Chat | — | ✓ | ✓ | — | — |
| Xcode | Copilot cloud agent | — | ✓ | ✓ | ✓ | — |
| Xcode | Copilot code review | — | ✓ | ✓ | — | — |
| Copilot CLI | All features | ✓ | ✓ | ✓ | ✓ | — |

### 4a. `CLAUDE.md` now reaches everything `AGENTS.md` reaches

The 2026-08-02 table had them differing in four places. They are now one type,
treated identically, with no footnote distinguishing them anywhere on the page.

**This weakens map constraint 4 but does not overturn it.** A repository holding
only `CLAUDE.md` is still invisible to: GitHub.com Chat, VS Code code review,
Visual Studio Chat and code review, JetBrains Chat and code review, Eclipse
Chat, Xcode Chat and code review. That is nine of eighteen rows. The silent
failure the whole toolkit exists to catch is real and roughly half the surface
area.

**`.github/copilot-instructions.md` remains the only entry supported in every
row that supports anything.** The central decision of ticket 04 survives intact.

### 4b. `AGENTS.md` is still dominated, for a new reason

Previously it was dominated because `copilot-instructions.md` reached strictly
more. It still is — but now `CLAUDE.md` is its exact equal on the Copilot side
while also being the only file Claude Code reads. So `AGENTS.md` is dominated
twice over. The "never ask for it, never penalise an existing one" rule stands.

### 4c. Path-specific instructions were missing from the skill entirely

`.github/instructions/**/*.instructions.md` is supported by thirteen of the
eighteen rows — more than the Agent type. The shipped `reach-matrix.md` has no
column for it and `survey-run.md` reads `.github/instructions/*` but the matrix
gives it nowhere to land.

**This is a false-negative generator.** A team that has organised its
instructions per path is told its agents cannot see anything. For a tool whose
whole framing is "help, not audit", telling a well-configured team they are
below the floor is the worst available error.

---

## 5. Skills — constraint 3 verified, and it holds

Copilot resolves agent skills from `.github/skills`, `.claude/skills` or
`.agents/skills` at project level, and `~/.copilot/skills` or `~/.agents/skills`
at personal level. Same `SKILL.md` format.

**Required frontmatter: `name` and `description`.** `name` must be lowercase
with hyphens. `license` is optional.

Supported by: cloud agent, code review, Copilot CLI, the GitHub Copilot app, and
agent mode in VS Code.

This was never verified before — it came from map constraint 3. It is correct.

**One consequence for the stub design:** stub `SKILL.md` files must carry both
`name` and `description`, not `description` alone, or Copilot rejects them the
way Claude Code rejected the frontmatter-less stubs in the first trial.

---

## 6. What this does not answer

Deliberately out of scope, per ticket 12's own split between what phase 1 needs
and what the translator needs:

- The full field-by-field loss analysis for Claude → Copilot conversion.
- Whether the loss is severe enough to reopen ticket 04.
- Copilot CLI's instruction-*generating* command (ticket 13, untouched).

---

## Sources

Primary vendor documentation and first-party repositories only. Retrieved
2026-08-14.

**GitHub Docs**
- *Support for different types of custom instructions* —
  https://docs.github.com/en/copilot/reference/custom-instructions-support —
  the authoritative matrix in section 4, and the five type-to-filename mappings.
- *Custom agents configuration* —
  https://docs.github.com/en/copilot/reference/custom-agents-configuration —
  frontmatter fields, `description` required, the `.md` / `.agent.md`
  deduplication note, preview status for JetBrains / Eclipse / Xcode.
- *Creating custom agents for Copilot cloud agent* —
  https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents
  — `my-agent.agent.md` in `.github/agents`.
- *Creating and using custom agents for GitHub Copilot CLI* —
  https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
  — `.github/agents/` and `~/.copilot/agents/`, `.agent.md`.
- *Adding agent skills for GitHub Copilot* —
  https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills
  — the three skills directories, required `name` and `description`, surfaces.
- *Prompt files* (customization library) —
  https://docs.github.com/en/copilot/tutorials/customization-library/prompt-files
  — `.prompt.md` in `.github/prompts`; public preview; VS Code, Visual Studio
  and JetBrains only.

**VS Code documentation**
- *Custom chat modes / custom agents* —
  https://code.visualstudio.com/docs/copilot/customization/custom-chat-modes —
  the `.chatmode.md` → `.agent.md` rename, `.github/agents/`,
  `~/.copilot/agents`, frontmatter list.
- *Prompt files* —
  https://code.visualstudio.com/docs/copilot/customization/prompt-files —
  frontmatter, `${input:…}` syntax, `#tool:` references, invocation.

**First-party repository**
- `github/awesome-copilot` — https://github.com/github/awesome-copilot —
  counted via `gh api "repos/github/awesome-copilot/git/trees/main?recursive=1"`
  on 2026-08-14: 226 paths matching `*.agent.md`, 0 matching `*.chatmode.md`,
  0 matching `*.prompt.md`. Repository root carries `.github/copilot-instructions.md`,
  `.github/agents/`, `.github/skills/` and a top-level `instructions/` collection.
