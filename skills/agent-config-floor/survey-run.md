# The survey run

Cheap, bounded, always first. It reads a fixed set of files, reports what the
agents cannot do here yet, and writes a stub for every artefact the repository
should have.

It must fit in one session on a 5,000-file monorepo, and two surveys of an
unchanged repository must produce the same gap list.

---

## Step 0 — the target

You should already have it from [SKILL.md](SKILL.md) step 1. If not, ask now.
Default `both`.

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

## Step 2 — detect the stack

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
- **No manifests at all means greenfield.** The interview in any later artefact
  run goes to greenfield mode. Say so in the report.

Which libraries deserve a skill of their own is a judgement about the library,
not a rule in a detector. Recommend a skill where a library is easy to misuse or
has a steep learning curve. Carry the evidence line every time.

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

Mark every finding that rests on criterion 3 (*specific*) as a **judgement
call**. Findings under criteria 1, 2 and 4 are facts.

---

## Step 5 — write the stubs

For every artefact the repository should have and does not, write a stub at its
correct path. Follow [reference/stubs.md](reference/stubs.md) exactly: the
paths, the one-line format, and the two rules.

Never overwrite a file that has real content. If a file exists and carries a
`TODO(floor):` marker, leave it as it is — it is already a stub.

---

## Step 6 — write the report

Write `.agent-floor/report.md`, following
[reference/report-template.md](reference/report-template.md) exactly.

The report states how many stubs remain unfilled. A repository sitting at eight
empty stubs is told so plainly and is not counted as having made progress.

**If the repository is already above the floor**, write three lines confirming
that and nothing else. No congratulations, no offer of further help.

---

## Step 7 — end the run

Tell the developer:

1. Where the report is.
2. The list of unfilled stubs, so they can pick one.
3. That filling one is a separate run, and they can do it whenever they like.

Then stop. Do not begin an artefact run unless the developer picks an artefact.

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
