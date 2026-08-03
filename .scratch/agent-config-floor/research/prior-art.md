# Prior art: build, fork, or wrap the existing tools

Resolves: `.scratch/agent-config-floor/issues/03-build-fork-or-wrap-existing-tools.md`
Researched: 2026-08-02. All facts cited in [Sources](#sources); anything unverified is labelled.

---

## 1. Verdict

**Build fresh.** Do not fork ClaudeForge, agent-docs-audit, or agent-audit. Wrap only the two
vendors' own generators, and only as the *remediation* step — never as the check.

The reasoning, in the order it decided the question:

1. **Every candidate is a quality tool. The floor is a presence question.** All three named tools
   begin from "a `CLAUDE.md` exists — how good is it?" ClaudeForge scores an existing file 0–100
   and enforces a line cap; agent-docs-audit scores out of 100 across six criteria and only audits
   root/nested files "when present"; agent-audit matches ~573 known-bad signatures against
   instruction surfaces that already exist. A repo at zero — the exact repo this effort exists to
   help — produces an empty report from all three, or nothing to run against at all. Presence is
   not a degenerate case of quality; it is a different query, over a different input (the reach
   matrix), producing a different output (which agent surface sees nothing).

2. **None of them knows GitHub Copilot's instruction layer, which is the whole point.** ClaudeForge's
   README does not mention Copilot, `copilot-instructions.md`, or `.github/` at all. agent-docs-audit
   audits `AGENTS.md` and `CLAUDE.md` only — and worse, it instructs the agent to "Prefer `AGENTS.md`
   as the canonical cross-harness file", which is false for Copilot: per GitHub's own support matrix,
   `AGENTS.md` reaches **no** Copilot Chat surface except VS Code, and reaches Copilot code review
   only on GitHub.com. agent-audit is a security scanner whose repo surfaces include `AGENTS.md`,
   `CLAUDE.md`, `SKILL.md` and MCP manifests, but its question is "does this file contain a prompt
   injection", not "does this file reach the agent". Adopting any of them leaves the map's stated
   real gap — the instructions asymmetry — untouched.

3. **Forking is worse than building.** The only candidate with real code worth inheriting is
   agent-audit (a genuine Python CLI). Its code is a rules engine for threat signatures; nothing in
   it maps onto a reach matrix. ClaudeForge and agent-docs-audit contain *no reusable code at all* —
   ClaudeForge is Claude Code skills, slash commands, an agent and three hook scripts; agent-docs-audit
   is a single `SKILL.md` of prose. There is no library to fork, only prompts. The floor check is
   perhaps a few hundred lines of "does this path exist, is the frontmatter valid, which of the two
   agents reads it" — cheaper to write than to inherit.

4. **The supply-chain rigour bar rules out adoption independently.** Under "treat a third-party skill
   like production software": ClaudeForge is a single-maintainer project (413 stars, MIT) whose
   install path is a Claude Code plugin that registers **hooks executing Python on every CLAUDE.md
   load and write** plus a background agent — persistent code execution in every developer session,
   for a cosmetic benefit. agent-audit is a single-maintainer, 15-star project installed via
   `git clone` + `pip install -e .` that **reads local session logs** — the highest-sensitivity
   artefact on a developer machine — with no PyPI-published, signed distribution verified.
   agent-docs-audit lives in a **1-star personal repo** (`pizzayap/pza-skills`), was **auto-scanned**
   into a third-party directory rather than curated, and its documented install
   (`npx skills add pizzayap/pza-skills`) installs *that whole repo's skills*, not one audited file.
   None of the three would survive an org software-intake review on maintenance signal alone.

5. **Two things are genuinely worth wrapping, and both are first-party.** Claude Code ships `/init`,
   which generates a starting `CLAUDE.md`, already reads `.github/copilot-instructions.md` and Cursor
   rules and folds relevant parts in, and — behind `CLAUDE_CODE_NEW_INIT=1` — runs a **multi-phase
   flow that asks which artefacts to set up: CLAUDE.md files, skills, and hooks**. That is
   startlingly close to this effort's own remediation phase, from the vendor, at zero supply-chain
   cost. On the Copilot side, VS Code ships `/init` and `/create-instruction`, and GitHub's Copilot
   cloud agent will generate `.github/copilot-instructions.md` as a draft PR. The floor toolkit
   should **detect the gap itself and then hand off to these**, rather than generating instruction
   prose of its own. This also satisfies "not standardisation": the vendor generator writes what the
   repo actually looks like; we never ship a template.

6. **One third-party tool deserves a watching brief, not adoption: Ruler.** 2,835 stars, MIT, active
   (pushed 2026-07-29), a real npm CLI. It is the only tool found that treats one instruction source
   and many agent output paths as the problem — which is the asymmetry, correctly framed. It is
   still wrong for this floor: it supports 15 agents where the map allows exactly two; per its
   README table it maps **GitHub Copilot to `AGENTS.md`**, not `.github/copilot-instructions.md`
   (the only path that reaches every Copilot surface); and it "automatically manages your
   `.gitignore` to keep generated agent configuration files out of version control" — if that
   applies to the Copilot instruction file, the file is invisible to Copilot cloud agent and Copilot
   code review, which read the committed repo. That is a silent-failure mode of exactly the kind the
   map warns about. Revisit if the floor later needs a distribution mechanism; do not build on it now.

**Net:** build a small, dependency-free presence checker keyed to a two-agent reach matrix; wrap
`/init` (both vendors) for remediation; adopt nothing.

---

## 2. Comparison table

| Tool | Presence or quality? | Claude only, or both agents? | Licence | Maintenance signal | Composable? |
|---|---|---|---|---|---|
| **ClaudeForge** | **Quality** — 0–100 score, 150-line cap, drift/link/dependency audits of an existing `CLAUDE.md`. Also *generates*. | **Claude only.** Reads `CLAUDE.md`, `CLAUDE.local.md`, `AGENTS.md`, `.cursorrules`, `.windsurfrules`. No mention of Copilot or `.github/`. | MIT | 413★, 52 forks, created 2025-11-12, pushed 2026-05-19 (~2.5 months stale). Single maintainer. Default branch `dev`. | **No.** Claude Code plugin only; README states no standalone CLI or library. Ships hooks + a background agent. |
| **agent-docs-audit** | **Quality** — six-criterion /100 score + AGENTS↔CLAUDE mirror drift. Read-only, never edits. | **Claude only** (plus generic "cross-harness"). Audits `AGENTS.md` + `CLAUDE.md`. No Copilot path. Asserts `AGENTS.md` is canonical — wrong for Copilot. | MIT (repo `pizzayap/pza-skills`) | **1★**, created 2026-04-30, pushed 2026-07-17. Personal repo. Listed on buildwithclaude.com as **"Auto-scanned"**, i.e. not curated. | **No.** A single `SKILL.md` of prose. No code, no CLI, no API. |
| **agent-audit** | **Neither** — security/forensics. Matches ~573 bundled signatures for known-bad patterns in configs, instruction files and session logs. | **Claude only** among our two (plus Codex CLI, OpenClaw). README does not mention `.github/copilot-instructions.md`. | MIT (GitHub reports `NOASSERTION` because of an appended third-party-notice paragraph; the `LICENSE` file is verbatim MIT, © 2026 Sergey Gordeychik / CyberOK) | 15★, 4 forks, created 2026-04-28, pushed 2026-07-15. Single maintainer; CHANGELOG + ROADMAP present. | **Yes** — real Python CLI (`agent-audit scan`, `scan-project`, cross-mode). Installed by `git clone` + `pip install -e .`. |
| **Ruler** (surfaced) | **Neither** — distribution. Writes one rule source out to 15 agents' config paths. | **Both, nominally** — but maps Copilot → `AGENTS.md` per README; `.github/copilot-instructions.md` appears only as a configurable `output_path` example in source. | MIT | **2,835★**, pushed 2026-07-29. Most active and most adopted of any candidate. | **Yes** — `npm i -g @intellectronica/ruler`; `ruler init` / `apply` / `revert`, `--dry-run`. |
| **Claude Code `/init` + `/doctor`** (first-party) | `/init` **generates**; `/doctor` is **quality** (proposes trims to a checked-in `CLAUDE.md`). Neither reports presence across agents. | **Claude only** — but `/init` *reads* `.github/copilot-instructions.md` and Cursor rules as input. Claude Code does **not** read `AGENTS.md`. | Anthropic product | Shipping product | Slash commands inside Claude Code. Not a library. |
| **VS Code / GitHub Copilot `/init`** (first-party) | **Generates**. No presence report. | **Copilot only.** | Microsoft/GitHub product | Shipping product | IDE commands + a cloud-agent flow that opens a draft PR. Not a library. |
| **dabit3/skill-audit** (surfaced) | **Quality** of *skill definitions* — security, completeness, cross-agent compatibility. | Multi-agent (Codex, Claude Code, OpenClaw). Not instructions. | MIT | 44★, pushed **2026-02-10** — ~6 months stale. | Not verified. |
| **Swader/agent-skills-audit** (surfaced) | Skills audit (not verified in detail). | Not verified. | **No licence declared** → not adoptable in an org regardless of merit. | 19★, pushed 2026-07-15, branch `master`. README not retrievable at the default raw path. | Not verified. |

---

## 3. Per-tool detail

### ClaudeForge — `alirezarezvani/ClaudeForge`

**What it does.** "Automated CLAUDE.md creation, enhancement, and maintenance for Claude Code
projects." Ships five skills (`claudeforge-skill`, `karpathy-guidelines`, `claude-md-drift-audit`,
`claude-md-link-check`, `claude-md-dependency-rescan`), three slash commands (`/enhance-claude-md`,
`/sync-claude-md`, `/claude-to-agents`), one background agent (`claude-md-guardian`, `model: haiku`,
fail-closed, never auto-commits), and three hook scripts including `hooks/validate-claude-md.py`
which enforces a **hard 150-line cap per `CLAUDE.md` at load and write time**.

**Presence or quality.** Quality, plus generation. Its scoring is 0–100 on completeness, formatting
and specificity of a file that exists. It does not check for the absence of any file outside the
`CLAUDE.md` family. Its generation path (`/enhance-claude-md`) *is* a zero-to-something route, but
only for Claude, and only for `CLAUDE.md`.

**Both agents?** No. Supported instruction formats are `CLAUDE.md`, `CLAUDE.local.md`, `AGENTS.md`,
`.cursorrules`, `.windsurfrules`. `/claude-to-agents` converts a CLAUDE.md tree to `AGENTS.md` in
three modes (`--symlink` default on macOS/Linux, `--copy`, `--inline-chain`). Nothing writes or reads
`.github/copilot-instructions.md`. Since `AGENTS.md` does not reach Copilot Chat outside VS Code, a
team that ran `/claude-to-agents` and believed itself covered would be wrong — the invisible-failure
pattern the map names.

**Safe to depend on?** MIT, which is fine. The concern is operational, not legal: the recommended
install registers hooks that run Python on every load and write of a `CLAUDE.md`, plus a background
agent, in every developer's session. That is persistent third-party code execution across the org.
Single maintainer; last push 2026-05-19. Default branch is `dev`, so the marketplace/`main` install
path and the documented feature set may diverge — **not verified which branch `/plugin install`
resolves**.

**Composable?** No. README explicitly: no standalone CLI or library entrypoint; non-Claude-Code usage
unsupported. Cannot be called from a script, a platform-team tool, or a Copilot context.

**Direct conflict with the map.** The 150-line hard cap, enforced by splitting files, is a
standardisation mechanism. Constraint 2 says teams diverge upward freely and house style is never
standardised. Adopting ClaudeForge would import a house style into the floor.

### agent-docs-audit — skill in `pizzayap/pza-skills`

**What it does.** A read-only `SKILL.md`. Discovers root `AGENTS.md` + `CLAUDE.md` and nested
`*/AGENTS.md`, `*/CLAUDE.md` (flags: `--root-only`, `--all`, or a path argument). Verifies claims
against the repo (`package.json`, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `rg --files`)
rather than trusting prose. Scores /100 across Commands/workflows (20), Architecture clarity (20),
Non-obvious patterns (15), Conciseness (15), Currency (15), Actionability (15), graded A–F. Then
checks AGENTS↔CLAUDE **mirror drift**, distinguishing intentional differences from accidental ones.
Explicitly marks unverifiable claims `Unverified` instead of guessing — a good habit worth stealing.

**Presence or quality.** Quality. Presence enters only as "audit root files *when present*"; a repo
with neither file yields an audit of nothing. It also declines to edit, by design — so it is not a
remediation path either.

**Both agents?** No. `AGENTS.md` and `CLAUDE.md`. Its harness list is "Codex/OpenCode/Pi/Claude". No
Copilot surface, no `.github/` path, no `.github/instructions/`. Its instruction to "Prefer
`AGENTS.md` as the canonical cross-harness file. Treat `CLAUDE.md` as a Claude Code compatibility
mirror" is actively misleading for a Copilot shop.

**Safe to depend on?** Licence MIT via the source repo. But: 1 star, created three months ago, a
personal skills collection, surfaced through a third-party directory that labels the entry
"Auto-scanned" — meaning the directory scraped it, not vetted it. Install is
`npx skills add pizzayap/pza-skills`, which brings the repo's skills, not one reviewed file. Under
production-software rigour this fails intake. If any part of it is wanted, **copy the six-criterion
rubric as prose under our own licence review** — do not install the skill.

**Composable?** No. Prose instructions for an interactive agent. No CLI, no importable code.

### agent-audit — `scadastrangelove/agent-audit`

**What it does.** "Forensic auditor for local AI coding agents (Claude Code, Codex CLI, OpenClaw)
and project-surface scanner for repos containing skills, plugins, and MCP manifests." Three modes:
`scan` (local agent homes, session traces, configs), `scan-project` (repo instruction surfaces —
`SKILL.md`, `AGENTS.md`, `CLAUDE.md`, plugin and MCP manifests, tool descriptions, task YAML), and a
cross-mode linking static repo findings to dynamic session findings. As of v0.21.0 it ships ~573
normalised imported rules (104 ATR, 180 Aguara-derived, 26 Cisco PromptGuard-derived, 217
Gitleaks-derived, 46 NOVA-derived) plus native detectors and optional Cisco MCP YARA signatures.

**Presence or quality.** Neither. It is a threat scanner: it asks whether existing config contains
known-bad patterns (injection, secrets), not whether config exists or reaches an agent. Its
Gitleaks-derived rules make it a decent *secrets-in-instruction-files* check, which is a real but
separate concern.

**Both agents?** No — Claude Code, Codex CLI, OpenClaw. Codex CLI and OpenClaw are explicitly out of
scope for this effort. No `.github/copilot-instructions.md` handling in the README.

**Safe to depend on?** Licence is genuinely MIT (GitHub's API reports `NOASSERTION` only because a
third-party-notice paragraph is appended to the standard text — verified by reading `LICENSE`).
Maintenance is thin: 15 stars, 4 forks, three months old, single maintainer, though CHANGELOG and
ROADMAP exist. The install path is `git clone` + `pip install -e .` from a git remote — an editable
install of unpinned source, not a signed release artefact. **Not verified whether it is published to
PyPI.** Most importantly, `scan` reads local session logs; running it org-wide would be exactly the
unconsented scanning constraint 1 forbids, and would put developer session content through a
third-party rules engine.

**Composable?** Yes — the only named tool that is. If the org ever wants a secrets/injection sweep of
instruction files under the *deferred final phase*, this is a reasonable thing to evaluate then, on
its own merits, as a security tool. It is not a floor tool.

### Ruler — `intellectronica/ruler` (surfaced; closest adjacent solution)

One `.ruler/` source of rules, applied out to 15 agents. `ruler init` scaffolds; `ruler apply`
writes; `ruler revert` undoes; `--dry-run` previews; `.bak` backups before overwrite, on by default.
Also distributes MCP server settings. MIT, 2,835 stars, pushed 2026-07-29 — by a wide margin the
healthiest project in this space.

Why it is still not the answer: (a) its README's agent table maps **GitHub Copilot → `AGENTS.md`**;
`.github/copilot-instructions.md` appears in the source only as a commented `output_path` example in
`src/cli/handlers.ts` and `test-qa/.ruler/ruler.toml`, i.e. it is configurable but not the default —
**exact default not verified beyond the README table**. (b) It "automatically manages your
`.gitignore` file to keep generated agent configuration files out of version control." Copilot cloud
agent and Copilot code review read the committed repository; a gitignored instruction file reaches
neither. **Whether this applies to the Copilot output path, and whether it can be disabled, is not
verified** — but it is a specific failure mode to test before ever recommending Ruler. (c) It is a
distribution tool. It answers "keep these in sync", not "does this repo have any". The floor needs
the second question answered before the first one is meaningful.

### First-party tooling (Anthropic and GitHub)

**Anthropic / Claude Code.**
- `/init` generates a starting `CLAUDE.md` by analysing the codebase; if one exists it suggests
  improvements rather than overwriting. It **already reads Cursor rules and
  `.github/copilot-instructions.md`** and incorporates relevant parts.
- With `CLAUDE_CODE_NEW_INIT=1`, `/init` becomes an interactive multi-phase flow that **asks which
  artefacts to set up — CLAUDE.md files, skills, and hooks** — explores with a subagent, asks
  follow-ups, and presents a reviewable proposal before writing. It additionally reads `AGENTS.md`,
  `.devin/rules/`, `.windsurf/rules/`, `.clinerules`.
- `/doctor` (v2.1.206+) proposes trims for a checked-in `CLAUDE.md`, cutting content derivable from
  the codebase and keeping pitfalls and rationale. Quality, not presence.
- `/memory` lists CLAUDE.md locations **including ones that don't exist yet**, and creates a file on
  selection — the closest thing to a first-party presence signal, but interactive and Claude-only.
- `/context` shows which memory files actually loaded — the correct primitive for "is it in a path
  the agent reads", which is the floor's real question.
- Claude Code **does not read `AGENTS.md`**; the documented bridge is a `@AGENTS.md` import inside
  `CLAUDE.md`, or a symlink.
- No first-party tool checks presence across agents. Nothing Anthropic ships knows about Copilot's
  reach.

**GitHub / Microsoft.**
- Three repository instruction types: `.github/copilot-instructions.md`,
  `.github/instructions/NAME.instructions.md`, and agent files (`AGENTS.md` anywhere; `CLAUDE.md`
  and `GEMINI.md` root only).
- Generation exists: VS Code's `/init` and `/create-instruction`, plus `Chat: New Instructions File`
  and `Chat: Configure Instructions`; and on github.com the Copilot cloud agent can generate a
  `.github/copilot-instructions.md` and open it as a draft PR.
- **No first-party validator.** GitHub's docs describe generation, not checking.
- `github/awesome-copilot` (37,361★, MIT, pushed 2026-08-01) is a community *content* collection —
  instructions, agents, skills, configurations — not a checker. Useful as a source of example
  content for the remediation step; irrelevant as a floor tool.

---

## 4. What the floor needs that none of them provide

1. **A presence verdict, not a score.** "This repo has `CLAUDE.md` and no Copilot instruction file"
   is the output. Every tool here produces a grade. A grade on a file that doesn't exist is not a
   number, and a team at zero cannot act on one.

2. **A two-agent reach matrix as the unit of evaluation.** The floor's question is not "which files
   exist" but "which of the two agents' surfaces currently see nothing." That requires encoding
   GitHub's published support matrix as data. No tool found encodes it. Concretely, from GitHub's
   own reference (reproduced in Sources):
   - `.github/copilot-instructions.md` is the **only** path that reaches every Copilot surface (the
     single exception is Eclipse code review, which supports nothing).
   - `AGENTS.md` reaches: GitHub.com cloud agent + code review, VS Code Chat + cloud agent, and the
     cloud agent in JetBrains/Eclipse/Xcode, and Copilot CLI. It reaches **no** Copilot Chat in
     Visual Studio, JetBrains, Xcode, or on github.com, and **no** code review outside github.com.
   - `CLAUDE.md` reaches: every cloud agent surface, **and Copilot CLI (all features)**. Not Chat
     outside cloud agent, not code review.
   **Two corrections for the map, flagged not relitigated:** (a) constraint 4 says `CLAUDE.md`
   reaches "Copilot cloud agent only" — per GitHub's matrix, Copilot CLI reads it too; (b) it says
   `AGENTS.md` reaches "cloud agent + VS Code chat + code review" — code review support is
   github.com-only, and VS Code is the only Chat surface. The asymmetry conclusion is unchanged and
   arguably sharper; the specific cells are not right.

3. **A zero-install path.** Every candidate requires installing third-party software *before* the
   team can learn it is below the floor. Under both constraint 1 (invitational, self-serve, no
   unconsented scanning) and the production-software intake bar, the check itself must run with
   nothing installed — a script or a repo-local file, reviewable in one sitting. That requirement
   alone eliminates the plugin-and-skill form factor that ClaudeForge and agent-docs-audit are
   built on.

4. **Non-destructive by construction.** The floor must never rewrite house style. ClaudeForge's
   150-line cap splits files; Ruler overwrites agent config from a central source and gitignores the
   result. Both are reasonable products and both would violate constraint 2 if adopted as the floor.

5. **Skills presence, cheaply.** The floor should confirm a skills directory exists in one of the
   three paths both agents resolve, and that each `SKILL.md` has valid frontmatter. No named tool
   checks skills *presence*; the skills tools found (`dabit3/skill-audit`,
   `Swader/agent-skills-audit`) check quality and security of skills that already exist, and one of
   them has no licence.

6. **The same code in both modes.** Self-serve and assisted must run the identical check, differing
   only in who reads the output. Interactive skills cannot do this: they need a human at a Claude
   Code prompt. A CLI can be run by a team alone, by the platform team when invited, and later by
   the deferred final phase — without any of those depending on the others.

7. **Honest "unverified".** Worth stealing from agent-docs-audit: mark a claim `Unverified` rather
   than reporting it as a failure. A floor report that cries wolf on a repo it couldn't parse will
   not be run twice.

**What is genuinely already solved, and should not be rebuilt:** generating instruction content.
Claude Code's `/init` (especially `CLAUDE_CODE_NEW_INIT=1`) and Copilot's `/init` /
`/create-instruction` / cloud-agent draft-PR flow already do this, from the vendors, tuned to their
own agent, at no supply-chain cost. The toolkit's remediation step should be "run these two
commands, here is what to check afterwards" — not a generator of our own.

---

## Sources

Primary sources only; each claim above traces to one of these. Retrieved 2026-08-02.

**Repositories (metadata via authenticated GitHub API, `gh api repos/...`)**
- ClaudeForge — https://github.com/alirezarezvani/claudeforge — MIT; 413★; 52 forks; created
  2025-11-12; pushed 2026-05-19; default branch `dev`; not archived. README:
  https://raw.githubusercontent.com/alirezarezvani/ClaudeForge/dev/README.md
- agent-audit — https://github.com/scadastrangelove/agent-audit — 15★; 4 forks; created 2026-04-28;
  pushed 2026-07-15; API reports licence `NOASSERTION`. `LICENSE` read directly via
  `gh api repos/scadastrangelove/agent-audit/contents/LICENSE`: verbatim MIT, © 2026 Sergey
  Gordeychik / CyberOK, with an appended third-party-notice paragraph (which is why the API cannot
  classify it).
- agent-docs-audit skill page — https://buildwithclaude.com/skill/agent-docs-audit (full skill text
  retrieved; page blocks automated fetchers, retrieved with a browser user-agent). Source repo
  https://github.com/pizzayap/pza-skills — MIT; **1★**; 0 forks; created 2026-04-30; pushed
  2026-07-17. Directory page labels the entry "Auto-scanned"; install shown as
  `npx skills add pizzayap/pza-skills`.
- Ruler — https://github.com/intellectronica/ruler — MIT; 2,835★; pushed 2026-07-29. README:
  https://raw.githubusercontent.com/intellectronica/ruler/main/README.md. The
  `.github/copilot-instructions.md` string was located via `gh search code` in
  `src/cli/handlers.ts` and `test-qa/.ruler/ruler.toml`, in both cases inside a commented-out
  `output_path` example.
- github/awesome-copilot — https://github.com/github/awesome-copilot — MIT; 37,361★; pushed
  2026-08-01.
- dabit3/skill-audit — https://github.com/dabit3/skill-audit — MIT; 44★; pushed 2026-02-10.
- Swader/agent-skills-audit — https://github.com/Swader/agent-skills-audit — **no licence declared**;
  19★; pushed 2026-07-15; default branch `master`; no description.

**Official documentation**
- GitHub, *Support for different types of custom instructions* —
  https://docs.github.com/en/copilot/reference/custom-instructions-support — the authoritative
  support matrix. Retrieved verbatim:

  | Copilot feature | `.github/copilot-instructions.md` | `.github/instructions/*.instructions.md` | `AGENTS.md` | `CLAUDE.md` | `GEMINI.md` |
  |---|---|---|---|---|---|
  | GitHub.com — Copilot Chat | Yes | No | No | No | No |
  | GitHub.com — Copilot cloud agent | Yes | Yes | Yes | Yes | Yes |
  | GitHub.com — Copilot code review | Yes | Yes | Yes | No | No |
  | VS Code — Copilot Chat | Yes | Yes | Yes | No | No |
  | VS Code — Copilot cloud agent | Yes | Yes | Yes | Yes | Yes |
  | VS Code — Copilot code review | Yes | No | No | No | No |
  | Visual Studio — Copilot Chat | Yes | Yes | No | No | No |
  | Visual Studio — Copilot code review | Yes | No | No | No | No |
  | JetBrains — Copilot Chat | Yes | Yes | No | No | No |
  | JetBrains — Copilot cloud agent | Yes | Yes | Yes | Yes | Yes |
  | JetBrains — Copilot code review | Yes | Yes | No | No | No |
  | Eclipse — Copilot Chat | Yes | No | No | No | No |
  | Eclipse — Copilot cloud agent | Yes | Yes | Yes | Yes | Yes |
  | Eclipse — Copilot code review | No | No | No | No | No |
  | Xcode — Copilot Chat | Yes | Yes | No | No | No |
  | Xcode — Copilot cloud agent | Yes | Yes | Yes | Yes | Yes |
  | Xcode — Copilot code review | Yes | Yes | No | No | No |
  | Copilot CLI — all features | Yes | Yes | Yes | Yes | Yes |

- GitHub, *Adding repository custom instructions for GitHub Copilot* —
  https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions
  — the three instruction types; cloud-agent generation via github.com/copilot/agents producing a
  draft PR; path-specific instructions on github.com limited to cloud agent + code review. No
  validation tool documented.
- GitHub, *Response customization* — https://docs.github.com/en/copilot/concepts/response-customization
  — links to the matrix above; does not contain it.
- VS Code, *Customize chat responses with custom instructions* —
  https://code.visualstudio.com/docs/copilot/customization/custom-instructions — `/init`,
  `/create-instruction`, `Chat: New Instructions File`, `Chat: Configure Instructions`; settings
  `chat.useAgentsMdFile`, `chat.useClaudeMdFile`, `chat.useNestedAgentsMdFiles`,
  `chat.includeApplyingInstructions`, `chat.instructionsFilesLocations`,
  `github.copilot.chat.organizationInstructions.enabled`.
- Anthropic, *How Claude remembers your project* — https://code.claude.com/docs/en/memory — `/init`
  behaviour and the `CLAUDE_CODE_NEW_INIT=1` multi-phase flow; `/init` reads
  `.github/copilot-instructions.md` and Cursor rules; `/doctor` trim proposals (v2.1.206+);
  `/memory` and `/context`; "Claude Code reads `CLAUDE.md`, not `AGENTS.md`"; CLAUDE.md locations
  (managed policy `/Library/Application Support/ClaudeCode/CLAUDE.md`, `/etc/claude-code/CLAUDE.md`,
  `C:\Program Files\ClaudeCode\CLAUDE.md`; user `~/.claude/CLAUDE.md`; project `./CLAUDE.md` or
  `./.claude/CLAUDE.md`; local `./CLAUDE.local.md`).

**Explicitly not verified** (stated rather than inferred)
- Which branch `/plugin install claudeforge` resolves to, given the repo's default branch is `dev`
  while the install scripts are fetched from `main`.
- Whether `agent-audit` is published to PyPI, or is git-install-only.
- Ruler's *default* Copilot output path in the current release: the README table says `AGENTS.md`;
  `.github/copilot-instructions.md` appears only as a commented example. Not confirmed from
  released code.
- Whether Ruler's automatic `.gitignore` management covers the Copilot instruction path, and whether
  it can be disabled. This is the specific thing to test before Ruler is ever recommended.
- The contradiction between VS Code's documented `chat.useClaudeMdFile` setting and GitHub's matrix
  row "VS Code — Copilot Chat / `CLAUDE.md` = No". Two first-party sources disagree; the default
  state of that setting was not established. Left open deliberately.
- `Swader/agent-skills-audit`'s functionality — its README was not retrievable at the default raw
  path. It is listed only to record that it exists and carries no licence.
- Whether `agent-docs-audit`'s skill text on buildwithclaude.com matches the current
  `pizzayap/pza-skills` source; the directory entry is auto-scanned and may lag.
