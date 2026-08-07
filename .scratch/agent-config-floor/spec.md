# Spec: the agent config floor toolkit

**Status:** ready to implement. Phase 1 (Bootstrap) only.
**Derived from:** the decisions in [map.md](map.md). Where this spec and a ticket disagree, the ticket is the record of *why* and this spec is the record of *what to build*.

---

## 1. What is being built

One **skill** with two kinds of run.

**The survey run** (cheap, bounded, always first):

1. reads a fixed, bounded set of files,
2. reports what Claude Code and GitHub Copilot cannot do in this repo yet,
3. writes a **marked stub** at the correct path for every artefact the repo should have.

**An artefact run** (opt-in, one per artefact, repeatable):

1. reads only the directories that artefact concerns,
2. interviews the developer **only where the code is ambiguous**,
3. writes the real artefact, replacing its stub,
4. re-checks its own output against the code before finishing.

The team works through the stubs one at a time, at whatever pace suits them. Nothing forces them past the survey run.

There is **no CLI, no npm package, no Node dependency, no server, no telemetry**. The only prerequisite is an agent, which is the thing the toolkit exists to help the team adopt.

### Non-goals for phase 1

- **A skill catalogue of any size.** [Ticket 09](issues/09-where-recommended-skills-come-from.md) took it to zero: nothing is authored, everything is generated per repo. There is no shared content to build, own, or keep current.
- Measurement, telemetry, token accounting (the deferred final phase)
- The assisted-engagement offer
- Enforcement of any kind

---

## 2. Delivery and install

Published at a stable public URL. The developer pastes one line into Claude Code or Copilot:

> Fetch `<URL>` and follow it.

The agent retrieves the skill and runs it. Offline or no-network fallback: `git clone --depth 1 <repo>` and point the agent at the local path.

**Always-latest.** No pinning, no versioned install. The floor's version travels with the skill, so a team learns the floor moved by running the tool, never by being told.

**Ephemeral.** The skill does not install itself into the repo. Anything it writes is config the *team* owns. The toolkit's own artefacts — including the report — never count toward the floor.

**Never phones home.** Output is a local file. Sharing that file is the team's voluntary route into the assisted path.

---

## 3. The floor

Six capabilities, all required, ranked by what fails first ([ticket 01](issues/01-what-capabilities-make-up-the-floor.md)):

| # | Capability | Portable between agents? |
|---|---|---|
| 1 | How to build, test and run | content, not format |
| 2 | What the project is and where code lives | content, not format |
| 3 | Coding standards and conventions | content, not format |
| 4 | Skills | **yes** — one `SKILL.md` serves both |
| 5 | Saved prompts | **no** |
| 6 | Sub-agents / custom agents | **no** |

**Named mandatory artefact, whatever the stack:** a **code-review** skill or prompt. Mandatory to *have*; its content is the team's own.

### Paths ([ticket 04](issues/04-which-files-and-paths-satisfy-the-floor.md))

One file is authored, the rest generated from it and committed. `AGENTS.md` is never asked for (it is dominated), but an existing one counts for whatever the matrix says it reaches.

| Target | Authored | Generated + committed |
|---|---|---|
| Both (default) | `CLAUDE.md` | `.github/copilot-instructions.md` |
| Copilot only | `.github/copilot-instructions.md` | — |
| Claude only | `CLAUDE.md` | — |

Skills: `.claude/skills/` for both-or-Claude, `.github/skills/` for Copilot-only.
Prompts and sub-agents: authored at `.claude/commands/*.md` and `.claude/agents/*.md`, translated to Copilot's paths.

`@import` references in `CLAUDE.md` must be **flattened inline** when writing the Copilot file — Copilot will not follow them.

**Generate mandates these paths. Audit accepts any layout that demonstrably reaches the agents.** Failing a well-configured team over an unusual layout is the fastest way to make them stop running the tool.

---

## 4. The reading budget

**The budget is per run, never per repo.** This is what keeps every run bounded while still allowing the artefact runs to read real code.

### Survey-run budget

Fixed, named, does not grow with repo size. The agent reads **only**:

- every dependency manifest and lockfile found anywhere in the repo — `package.json`, `pyproject.toml`, `requirements.txt`, `go.mod`, `pom.xml`, `build.gradle`, `*.csproj`, `Gemfile`, `Cargo.toml`
- the directory tree to depth 2
- the README
- CI workflow files
- the existing agent-config files themselves

Plus **targeted lookups driven by claims** (section 5). No other reading. No recursion. No source-file scanning. The agent is instructed to stop and report `could not determine` rather than explore further.

This budget is what makes the survey fit in one session on a 5,000-file monorepo, and what makes two surveys read the same files.

### Artefact-run budget

Scoped by the artefact being built, and by nothing else:

| Artefact | Reads |
|---|---|
| React / front-end skill | the component directories |
| Backend / API skill | the API and handler layer |
| Database skill | schema, migrations, data-access layer |
| Coding standards | a sample across the directories the survey identified |
| Code-review artefact | the diff surface — recent commits and the files they touch |

The artefact scopes the read, so nothing is ever an open-ended scan. If the scoped directory is itself too large for one session, the agent samples and **says in its output that it sampled** — it does not silently read part and speak as if it read all.

### Stack detection ([ticket 10](issues/10-how-the-stack-is-detected.md))

- **Manifest presence is the trigger.** Each detected dependency is reported **with its evidence** (`package.json` lists `@tanstack/react-query`); the developer confirms or dismisses. Dev-only dependencies are labelled and ranked lower.
- **Monorepo = more than one manifest.** Recommendations are **per-package**, each labelled with its path. The **floor itself is per-repo** — agents read from the root.
- **No manifests = greenfield.** Interview goes to greenfield mode.
- Which libraries deserve a skill is **catalogue metadata**, never detector logic.

---

## 5. The gap-finder ([ticket 06](issues/06-how-the-floors-effect-gets-measured.md))

Two passes, one session. Not a judge — a gap-finder.

**Pass 1 — claim check.** Every factual claim in existing config becomes exactly one lookup:

| Claim in config | Lookup |
|---|---|
| "run `pnpm test`" | read `package.json` scripts |
| "code lives in `src/services`" | list that directory |
| "we use Prisma" | grep for the import |

Cost scales with what the config *says*, not with repo size.

**Pass 2 — omission sweep** over the fixed budget above, checking the six capabilities.

### Rubric — `floor.md`

One file, owned by the toolkit, read in **both** directions: the gap-finder checks against it and the generator writes toward it. Written once so it cannot drift. Four criteria per capability, in order:

1. **Present** — an artefact exists in a path the target agents actually read. **An artefact carrying a `TODO(floor):` marker counts as absent** (see section 6a).
2. **Well-formed** — parses; frontmatter valid where required
3. **Specific** — describes *this* repo. Generic filler counts as absent.
4. **True** — every claim survives the claim check

### Reproducibility

The **gap list must be stable** across runs; prose may vary. Bought by construction, not by hope:

- checks are lookups with one answer, not opinions
- the reading budget is a fixed named set
- the output has a fixed schema
- a strict system prompt that forbids exploration beyond the budget

Criterion 3 (*specific*) genuinely needs judgement. Findings under it are **marked as judgement calls** in the output. A team is never told a judgement call is a fact.

**Stated limits, printed in the report body, not buried:**

- Advisory. Not a certification. Gates nothing.
- Confirms build and test commands are *written down*, not that they work.
- Judges gaps against what the repo declares about itself. A repo that lies in its own manifests is judged on the lie.

### Self-grading

The same agent generates and checks, deliberately. One rule breaks the loop: **after generating, re-run the claim check against the code. Any claim the agent cannot verify is written into the artefact as a `TODO(floor):` question to the developer, never as an assertion.**

---

## 6. The report ([ticket 07](issues/07-what-the-floor-report-looks-like.md))

**Path:** `.agent-floor/report.md` (stable; the team chooses whether to commit it; their git history becomes the only record).

**Framing: "what your agents cannot do here yet."** Not a checklist. Not a gap report. **No score, no level, no percentage, no `3/6`.** A score invites comparison between teams, and comparison turns an invitation into a league table.

Every gap is written as a **capability sentence**, not a missing-file sentence:

> Claude Code cannot run your tests without asking, because no build or test command is written down anywhere it reads.

not

> MISSING: build command

### Format

````markdown
```yaml
date: 2026-08-06
floor_version: <version>
target: both
capabilities:
  build_test_run: absent | present | unverifiable
  project_and_layout: ...
  coding_standards: ...
  skills: ...
  saved_prompts: ...
  sub_agents: ...
```

## What your agents cannot do here yet

<capability sentences, ordered by what fails first>

## What exists but we cannot yet help you build

<the missing / not-yet-helpable split — never let a later phase read as a broken promise>

## Copilot surface reach

<computed from GitHub's published matrix, held as versioned data in the toolkit —
 "Visual Studio Chat sees nothing", not "a file exists">

## What this report does not tell you

<the three stated limits, verbatim>
````

Status uses `absent`, never `fail`.

The header is fenced YAML in the **same file** as the prose — a split data file and human file would drift apart, and the final phase needs the header at a stable path with git history behind it. The tension between a machine-readable block and a helping tone is real and is mitigated by placement and wording, not dissolved.

**A repo already above the floor** gets a three-line confirmation and nothing else. No congratulations, no upsell.

**The report never counts toward the floor.**

---

## 6a. Marked stubs ([ticket 09](issues/09-where-recommended-skills-come-from.md))

The survey run writes a stub at the correct path for every artefact the repo should have. Chosen over a checklist in the report because a report is easy to ignore and a directory of files is not — a beginner sees the shape of a configured repo in the file tree, and an unfilled stub is visible in every pull request.

**Stub format — one line of content, nothing more:**

```markdown
TODO(floor): <what this artefact is for, in one sentence>. Unfilled — this file
contains no guidance. Run the floor skill and pick this artefact to fill it.
```

**Two rules, both mandatory:**

1. **`TODO(floor):` means absent.** The rubric never counts a marked stub as present. Without this the toolkit writes eight stubs and its own next run reports the repo as having skills — a false pass manufactured by our own hands. Ticket 05 established that every file the toolkit leaves behind counts toward the floor; this rule is what makes stubs safe under it.
2. **A stub tells the agent to ignore it.** An unfilled stub must cost a reading agent nothing beyond that one line. Stubs are the single exception to "the toolkit leaves nothing behind", and cheapness is the price of the exception.

**The report states how many stubs remain unfilled.** A repo stalled at eight empty stubs is told so plainly, not counted as progress.

---

## 7. The interview ([ticket 11](issues/11-how-the-interview-works.md), as amended)

The survey run asks almost nothing. Each **artefact run** has its own interview.

**The questioning rule: ask only where the code is ambiguous.** The generator reads the directories its artefact concerns, and where the codebase disagrees with itself — two files following different patterns, two styles of React usage, two ways of structuring a handler — it surfaces the disagreement and asks which one is the standard:

> `src/api/orders.ts` returns a bare object; `src/api/users.ts` wraps responses in `{ data }`. Which is the pattern to follow?

A repo that is internally consistent gets almost no questions. This replaces the earlier fixed list of six questions, which asked the same things of every repo including those whose code already answered them.

**Hard cap: six questions per artefact run.** Survivable only because runs are separate and opt-in — thirty questions in one sitting is the abandonment failure this rule exists to prevent.

Every pre-filled answer carries **its evidence, never a bare default and never a confidence score**:

> Test command — I read `pnpm test` from `package.json` scripts. Correct?

The survey run asks exactly one question, because it cannot be read from the code: **target** — Claude Code, Copilot, or both (default both).

### Rules

- **"I don't know" is an accepted answer** and becomes a `TODO(floor):` line. The tool assumes the reader is a **new joiner**, because that is who runs a setup tool. No question requires history the person may not have.
- **A half-finished interview always writes what it has.** Abandonment must never leave the repo worse than before. Unanswered items become explicit `TODO(floor):` lines naming the open question.
- **Resuming is re-running the skill** and picking the same artefact. It reads what exists and asks only what is still open.
- **Greenfield generates less on purpose.** Structure plus `TODO(floor):` lines, and the run ends by telling the developer to re-run once there is code. A thin honest file beats a rich wrong one, because the file is fed to an agent on every future session.

---

## 8. Generation

**One generator per artefact, never one that writes everything.** A single prompt covering instructions, skills, sub-agents and code review would be shallow at all four. Each generator knows what its artefact is for, which directories to read, and which ambiguities are worth asking about.

**Prefer the vendor's own generator** where one reaches the developer's surface — Claude Code's `/init`, VS Code's "Chat: Generate Instructions". The vendor reads a codebase better than our prompt does.

**Fallback: generate it ourselves** where no vendor generator reaches that surface (for example Copilot-only on JetBrains or Visual Studio). [Ticket 03](issues/03-build-fork-or-wrap-existing-tools.md)'s rule was "do not build a generator to compete with the vendors'"; a fallback for surfaces the vendors do not reach competes with nothing. Reporting a hole instead of filling it would fail the destination.

**Generated instruction files carry one self-test line** — if asked whether project instructions are loaded, reply with a known token. Lets a developer confirm loading in their own surface in ten seconds, with no platform-team involvement.

**Copilot translation is best-effort in phase 1** and marked as such in the output. Placeholders (`$ARGUMENTS` vs `${input:name}`) and frontmatter keys differ; the conversion is lossy and the loss is accepted, not solved. [Ticket 12](issues/12-copilot-prompt-and-agent-formats.md) establishes exactly what is lost and sharpens this in phase 2.

---

## 9. Phasing ([ticket 08](issues/08-how-the-effort-is-phased.md), as amended)

| Phase | Content | Stands alone because |
|---|---|---|
| **1 — Bootstrap** | This spec. Gap-find and generate in one skill run, all six capabilities. Claude Code first, Copilot best-effort. | A repo at zero ends the run with working config. |
| **2 — Sharpen** | Stack-aware skill recommendations, Copilot translation done properly, interview refined from trial feedback. | Phase 1 already works. |
| **3 — Measurement** | The deferred final phase. Telemetry, quantitative. | Nothing before it depends on it. |

No dependency arrow points forward. The catalogue is not a phase and nothing may depend on it.

---

## 10. Known open items, none blocking phase 1

- [What Copilot's saved-prompt and custom-agent formats actually are](issues/12-copilot-prompt-and-agent-formats.md) — research; sharpens translation in phase 2.
- [Does Copilot CLI have an instruction-generating command](issues/13-copilot-cli-instruction-generation.md) — research; no longer on the critical path now that self-generation is an accepted fallback.
- [What telemetry can attribute token spend and quality to a repo](issues/02-what-telemetry-attributes-cost-and-quality-to-a-repo.md) — the final phase only.

---

## 11. First trial

The user will run phase 1 against repositories with **no agent config at all**. That trial is the reaction step for [the report](issues/07-what-the-floor-report-looks-like.md) and [the interview](issues/11-how-the-interview-works.md), both of which were resolved on design and deliberately left their tone and question-count to be corrected by real use rather than by a mock-up.

What to watch for and bring back:

- Did the report read as help or as an audit?
- Did the stubs read as helpful structure or as clutter? Did anyone actually fill one?
- Was six questions per artefact too many, or too few?
- Did discrepancy detection find real disagreements, or invent them?
- Did the generated config survive its own claim check, or did the agent assert things the repo contradicts?
- Did anything push the agent past the reading budget?
