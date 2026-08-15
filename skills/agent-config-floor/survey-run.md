# The survey run

Cheap, bounded, always first. It reads a fixed set of files, reports what the
agents cannot do here yet, writes a stub for every artefact the repository
should have, and lists everything else worth building.

It must fit in one session on a 5,000-file monorepo, and two surveys of an
unchanged repository must produce the same gap list.

---

## Step 0 — the target, and how you will write

You should already have the target from [SKILL.md](SKILL.md) step 1. If not, ask
now. Default `both`.

Read [reference/plain-english.md](reference/plain-english.md) once, now, before
you write anything at all. It governs every sentence this run produces — in the
session, in the report, in the backlog and in every stub. It is not optional and
it does not depend on which model is running this.

---

## Step 1 — the reading budget

**The budget is per run, never per repository.** Read **only** the following:

1. **Every dependency manifest and lockfile, anywhere in the repository.**
   `package.json`, `pyproject.toml`, `requirements.txt`, `setup.py`, `go.mod`,
   `pom.xml`, `build.gradle`, `build.gradle.kts`, `*.csproj`, `Gemfile`,
   `Cargo.toml`, `composer.json`, `mix.exs`, `Package.swift`, `pubspec.yaml`.
2. **The directory tree to depth 2.** No deeper.
3. **The README** at the repository root.
4. **CI workflow files** — `.github/workflows/*`, and the equivalent for
   whatever CI the repository uses.
5. **The existing agent-config files themselves** — `CLAUDE.md`,
   `.github/copilot-instructions.md`, `AGENTS.md`, `.cursorrules`,
   `.github/instructions/*`, and everything under `.claude/` and
   `.github/skills/`, `.github/prompts/`, `.github/agents/`.

Plus the **targeted lookups** in step 3, each one driven by a specific claim.

**Nothing else.** No recursion, no source-file scanning, no following
interesting-looking imports. When you cannot answer inside this budget, write
`could not determine` and move on.

If a directory listing at depth 2 is very large, list it and count it; do not
descend to make sense of it.

---

## Step 1b — the setup-command check

Do this before anything else, because it is the cheapest fix a team can make and
it changes what the rest of the report should say to them.

Each agent has its own command that writes a first instructions file for a
repository. Claude Code has `/init`. GitHub Copilot has `copilot init` and
`/init`. Follow [reference/init-commands.md](reference/init-commands.md): what
each command is, which files to look for, the three verdicts, and the exact
wording to use.

This check reads nothing new. Every file it looks at is already in the step 1
budget.

Record for each targeted agent: `done` or `not run`, and which file you looked
for. It goes at the top of the report in step 7.

**Two limits, both in that reference file, both important.** You cannot see
whether a person typed a command, so never state that they did not — state what
file you found or did not find. And this check never counts toward the floor: it
changes no capability's status.

---

## Step 2 — detect the stack and build the recommendation list

Three passes, all over the files you already read in step 1. **None of them
reads anything new.** Together they produce the ranked list that becomes
`.agent-floor/backlog.md` in step 6.

### 2a — skills, from the stack

**Manifest presence is the trigger.** For each manifest you read, record the
dependencies it declares.

Rules:

- **Every detected dependency is reported with its evidence.** Write
  "`package.json` lists `@tanstack/react-query`", never "you use TanStack
  Query". The developer confirms or dismisses; you do not measure real usage,
  because that needs reading the budget forbids.
- **Dev-only dependencies are labelled as such and ranked lower.**
- **More than one manifest means a monorepo.** Recommendations are then
  **per-package**, each labelled with the path of its manifest. The **floor
  itself stays per-repository**, because the agents read from the root.
- **No manifests at all means greenfield.** The interview in any later build run
  goes to greenfield mode. Say so in the report.

Which libraries deserve a skill of their own is a judgement about the library,
not a rule in a detector. Recommend a skill where a library is easy to misuse or
has a steep learning curve. Carry the evidence line every time.

### 2b — saved prompts, from repeated operations

A saved prompt is worth writing where the team **runs the same non-obvious
operation again and again**. Three places in the step 1 budget show that, and no
others:

| Evidence | What it suggests |
|---|---|
| A job in a CI workflow that is not build or test | release, publish, deploy, codegen, seed, backfill |
| A manifest script that is not `build`, `test`, `lint` or `start` | migrate, generate, sync, e2e, storybook |
| A README section giving a multi-step manual procedure | the procedure itself |

An operation that is one command with no arguments does **not** need a saved
prompt — the developer types the command. Recommend one where the operation has
steps, ordering, or preconditions a person has to remember.

### 2c — sub-agents, from shape

A sub-agent is worth handing work to when the work **reads a lot and returns a
little**, or needs a different set of tools. Judge that from the depth-2
directory listing and the counts you already took:

| Evidence | What it suggests |
|---|---|
| A directory far larger than its siblings | a read-only exploration agent scoped to it |
| A migrations directory with many files | a migration reviewer |
| A directory named for a sensitive area — `auth`, `billing`, `payments`, `security` | a review agent scoped to it |
| A published API surface — an SDK, a public package, an OpenAPI spec | a documentation agent |

### The rule that governs all three

**Do not invent an artefact to fill a capability.** If the evidence does not
support a recommendation, the list is short, and the backlog says so plainly. A
padded list is worse than a short one: it teaches the team that this tool
guesses.

Every recommendation, of every type, carries the evidence line that produced it.

### Rank the whole list

Rank all three types together, by **which artefact would most change what an
agent writes in this repository**. The mandatory code-review artefact ranks
first, because it is required whatever the stack.

The ranking is used twice: it decides which recommendations get a stub file in
step 5, and it is the order of the backlog in step 6.

---

## Step 3 — pass 1, the claim check

Every factual claim in the existing config becomes **exactly one lookup**. Cost
scales with what the config *says*, not with the size of the repository.

| Claim in the config | The one lookup |
|---|---|
| "run `pnpm test`" | read the `scripts` block of `package.json` |
| "code lives in `src/services`" | list that directory |
| "we use Prisma" | grep for the import |
| "migrations are in `db/migrate`" | list that directory |
| "the API is FastAPI" | grep the manifest for `fastapi` |

Record for each claim: the claim, the lookup, and what the lookup found —
`confirmed`, `contradicted`, or `could not determine`.

If the repository has no agent config at all, this pass is empty. That is
normal and is the case this skill exists for.

---

## Step 4 — pass 2, the omission sweep

Over the fixed budget from step 1, check each of the six capabilities against
[floor.md](floor.md). Apply the four criteria in order — present, well-formed,
specific, true — and stop at the first failure.

Use [reference/reach-matrix.md](reference/reach-matrix.md) to decide whether an
artefact reaches the target agents. **Accept any layout that demonstrably
reaches them.** Do not fail a team for an unusual but working layout.

Three layouts are easy to get wrong, so check them by name before concluding
that instructions are absent:

- `.github/instructions/**/*.instructions.md` — **counts.** Thirteen of the
  eighteen Copilot surfaces read it.
- `AGENTS.md` — **counts**, and reaches exactly what `CLAUDE.md` reaches.
- `.github/agents/<name>.chatmode.md` — **counts.** It is the old name for a
  custom agent.

Mark every finding that rests on criterion 3 (*specific*) as a **judgement
call**. Findings under criteria 1, 2 and 4 are facts.

---

## Step 5 — write the stubs

For every artefact the repository should have and does not, write a stub at its
correct path. Follow [reference/stubs.md](reference/stubs.md) exactly: the
paths, the format, the two rules, and how the cap counts.

Floor artefacts are never capped. Recommendations from step 2 are capped at
five stub files, taken in rank order.

**The cap bounds files on disk, never what the team is told.** Everything above
the cap goes into the backlog in step 6, in full, with its evidence. Nothing is
dropped.

Never overwrite a file that has real content. If a file exists and carries a
`TODO(floor):` marker, leave it as it is — it is already a stub.

---

## Step 6 — write the backlog

Write `.agent-floor/backlog.md`, following
[reference/backlog-template.md](reference/backlog-template.md) exactly.

**Every recommendation from step 2 gets a row**, of every type, whether or not
it got a stub file. This is the exhaustive list; the stub set is a sample of it.

If a backlog already exists, **merge into it — never replace it**. The merge
rules are in the template. A row the team added by hand survives untouched, and
no row is ever deleted.

---

## Step 7 — write the report

Write `.agent-floor/report.md`, following
[reference/report-template.md](reference/report-template.md) exactly.

The setup-command verdict from step 1b goes at the top, above the capabilities.
A team that has not run `/init` yet should read that line first, because running
it may fix three capabilities in one minute.

The report states how many stubs remain unfilled. A repository sitting at eight
empty stubs is told so plainly and is not counted as having made progress. The
report does not repeat the backlog; it points at it.

**If the repository is already above the floor**, write three lines confirming
that and nothing else. No congratulations, no offer of further help. Still write
the backlog: being above the floor does not mean there is nothing left worth
building.

---

## Step 8 — end the run

Tell the developer, in plain English:

0. **If step 1b came back `not run` for either agent, say this first**, before
   the report and before the backlog. Name the command to type, and say what it
   will write. One or two sentences, no more.
1. Where the report is, and where the backlog is.
2. The list of unfilled stubs, so they can pick one.
3. That filling one is a separate run, done by a separate skill, and they can do
   it whenever they like. Give them the line from
   [SKILL.md](SKILL.md), "Filling an artefact".

Then stop. Do not begin filling an artefact yourself.

---

## Why this run is reproducible

The gap list must be stable across runs; the prose around it may vary. That is
bought by construction, not by hope:

- the checks are lookups with one answer, not opinions
- the reading budget is a fixed named set
- the output has a fixed schema
- exploration beyond the budget is forbidden

Criterion 3 genuinely needs judgement, so its findings are disclosed as
judgement calls rather than pretending to be reproducible.

**The recommendation list is not held to this standard, and must not claim to
be.** The gap list is what has to be stable — a team told twice that they are
missing different things has been told nothing. The backlog is a set of
suggestions, it rests on judgement in passes 2b and 2c, and two runs may
reasonably rank it differently or surface different rows.

That is why the backlog **merges rather than replaces**: a row that appeared
once is kept, so variation between runs adds to the list instead of churning it.
