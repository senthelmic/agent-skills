# floor.md — the rubric

**Floor version: 1.0.0.**

This is one file read in two directions. The survey run checks a repository
against it. Every generator writes toward it. Keeping it in one file is what
stops the check and the generation from slowly disagreeing with each other.

**The generators live in the sibling skill, `agent-artefact-builder`, and read
this file across the directory boundary rather than keeping a copy.** Two copies
of a rubric is exactly the drift this file exists to prevent, so if the two
skills are ever separated, this file travels with both or the split is wrong.

---

## The four criteria

Apply them **in this order** to each capability. Stop at the first one that
fails; that is the reason the capability is not met.

### 1. Present

An artefact exists at a path the target agents actually read. Use
[reference/reach-matrix.md](reference/reach-matrix.md) to decide what "actually
read" means for the chosen target — never bare file presence.

**An artefact carrying a `TODO(floor):` marker counts as absent.** This rule has
no exceptions. Without it, this skill writes eight stubs and its own next run
reports the repository as having skills — a false pass manufactured by our own
hands.

### 2. Well-formed

It parses. Where frontmatter is required (skills, saved prompts, sub-agents),
the frontmatter is valid and has the required keys.

### 3. Specific

It describes *this* repository. Generic filler counts as absent.

Filler looks like: "Write clean, maintainable code." "Follow best practices."
"Use meaningful variable names." None of that tells an agent anything it did not
already assume.

Specific looks like: "Run `pnpm test --filter web`." "Handlers live in
`src/api/` and return `{ data }`, never a bare object." "Migrations are
generated, never hand-edited."

**This criterion needs judgement, and judgement is not reproducible.** Every
finding under criterion 3 must be marked in the report as a judgement call. A
team is never told a judgement call is a fact.

### 4. True

Every factual claim survives the claim check (see
[survey-run.md](survey-run.md), pass 1). A claim that fails its lookup makes the
capability fail this criterion, and the report says which claim and what the
lookup found.

---

## The six capabilities

For each: what it must contain to pass, and what to look at.

### 1. Build, test and run

**Passes when** the repository states, in a file the target agents read, how to
install dependencies, how to run the test suite, and how to start the
application (where there is one to start). Commands are written literally, not
described.

**Claim check:** every command mentioned is looked up in the manifest that would
define it — `package.json` scripts, `pyproject.toml`, `Makefile`, `go.mod`,
`Gemfile`, `Cargo.toml`, the CI workflow.

**Stated limit:** passing means the commands are *written down*. It does not
mean they work. This skill never runs them.

### 2. What the project is, and where the code lives

**Passes when** the artefact says in a few sentences what the project does, and
names the directories that matter with what each one holds. In a monorepo it
names the packages.

**Claim check:** every path named is listed. A named path that does not exist
fails criterion 4.

### 3. Coding standards and conventions

**Passes when** the artefact records conventions this repository actually
follows and that an agent would otherwise get wrong — error handling, the shape
of an API response, naming, test layout, what is generated and must not be
hand-edited.

**Claim check:** each stated convention is spot-checked against one or two files
in the directory it claims to govern.

This is the capability most likely to fail criterion 3. Be strict about filler
here and lenient about length. Four true specific rules beat thirty generic
ones.

### 4. Skills

**Passes when** at least one real skill exists at a skills path the target
reads, with valid frontmatter, describing something specific to this repository
or its technology stack.

**A code-review skill or saved prompt is mandatory by name.** If the repository
has no code-review artefact under capability 4 or capability 5, capability 4
fails, whatever else exists.

Which further artefacts a repository should have — skills, saved prompts and
sub-agents alike — comes from [survey-run.md](survey-run.md) step 2, and lands
in `.agent-floor/backlog.md` in full. Recommendations are recommendations; only
the code-review artefact is mandatory, and **nothing in the backlog affects any
capability's status.**

### 5. Saved prompts

**Passes when** at least one saved prompt exists at the path the target agent
reads, per targeted agent.

**Not portable.** Targeting both agents means this capability must be satisfied
twice — once at `.claude/commands/`, once at Copilot's prompt path. Targeting
one agent makes the floor materially smaller here. Say so plainly in the report
if the team is targeting both; the expensive default should not be a surprise.

### 6. Sub-agents / custom agents

**Passes when** at least one sub-agent definition exists at the path the target
agent reads, per targeted agent, with valid frontmatter and a description that
says when to use it.

**Not portable.** Same doubling as capability 5.

---

## The setup-command check is not a capability

Each agent has its own command that writes a first instructions file — Claude
Code's `/init`, Copilot's `copilot init`. The survey run checks whether that
command's output is here, and the report says so before anything else. See
[reference/init-commands.md](reference/init-commands.md).

**It is not a seventh capability.** It never counts toward the floor, it has no
status value in the list below, and it never changes any capability's status. It
is there because running it is usually the fastest way to move capabilities 1, 2
and 3 from `absent` to `present`, and a team deserves to be told the cheap fix
first.

---

## Status values

Report each capability as exactly one of:

- `present` — all four criteria met.
- `absent` — criterion 1, 2, 3 or 4 failed. Never write `fail`.
- `unverifiable` — the artefact exists but the check could not be completed
  inside the reading budget. Say what stopped it.

There is no partial status, no score, and no total.

---

## Two rules that keep this honest

**Generate mandates paths. Checking accepts any layout that demonstrably
reaches the agents.** When this toolkit writes a file it writes it at the path in
[reference/paths.md](reference/paths.md). When it checks a repository it accepts
whatever layout the reach matrix says works. Failing a well-configured team over
an unusual layout is the fastest way to make them stop running this.

**After generating anything, re-run the claim check against the code.** The same
agent both writes and checks, deliberately. This one rule is what breaks the
loop: any claim the agent cannot verify is written into the artefact as a
`TODO(floor):` question to the developer, never as an assertion.
