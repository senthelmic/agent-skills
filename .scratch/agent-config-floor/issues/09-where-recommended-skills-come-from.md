# Where the recommended skills come from

Type: grilling
Status: resolved

## Question

The tool recommends skills based on the project's stack — a React skill, a TanStack Query skill, a database skill. Every one of those must **exist** before it can be recommended. Decide where they come from.

This is the largest unscoped risk on the map. Recommending is a tooling problem and is nearly solved; *having something worth recommending* is a content problem with no end date.

The options, each with the objection that has to be answered:

- **We author them.** The platform team writes and maintains a real catalogue. Quality is controlled and consistent. But a React skill written today is wrong in eighteen months, and the catalogue grows with every library any team adopts. This is a permanent staffing commitment, not a project. Who owns it, and what happens when they leave?
- **We curate external ones.** Point teams at public marketplaces and repos. Nearly free. But the prior-art research set a hard bar — a third-party skill deserves the same intake rigour as production software — and most public skills are single-maintainer repos with a handful of stars. Curating means auditing each one, then re-auditing when it updates. Cheaper than authoring only if the audit is cheap, and it is not.
- **We generate them per repo.** The tool writes a TanStack Query skill tailored to *this* codebase, using the LLM. Infinite coverage, zero maintenance, and it fits "converge on technology, diverge on team" awkwardly well. But quality is unverifiable, you get thirty different React skills across the org, and a generated skill is exactly the "skill that exists to satisfy a checklist" the floor ticket warned against.
- **Hybrid.** Author a small core, generate the long tail. Decide the boundary and who moves things across it.

Pressure to apply:

- **What happens when a team's stack has no catalogue entry?** An internal framework, COBOL, something niche. The floor makes skills mandatory. If nothing can be recommended, that team cannot clear a floor they are told is the minimum — the tool fails hardest for the teams furthest from the mainstream, which inverts the whole purpose.
- **How does a recommended skill get updated once installed?** If a team copies it in, they own a fork and it rots. If they subscribe, that is a distribution mechanism nobody has built.
- **What stops the catalogue from becoming the standardisation this effort forbids?** Constraint 10 says technology skills are shareable and working-style skills are house style. Does that line hold under pressure, and who polices it?
- **Does the catalogue need to exist in phase one?** Or can phase one recommend *that* a team needs a React skill without supplying one? Ugly, but it decouples the tooling from the content problem entirely.

**One artefact is already mandatory by name: a code-review skill or prompt**, required in every repo regardless of stack (see [What capabilities make up the floor](01-what-capabilities-make-up-the-floor.md), "Named mandatory artefacts"). Whichever sourcing option wins must deliver at least this one. If the answer is "we author nothing", this artefact is the counter-example that has to be handled anyway — so it is a useful test case for each option: work out how a code-review skill would be produced, updated and kept team-specific under each, and the weakest option usually fails there first.

Resolution is the source, the ownership, and the answer for uncovered stacks.

## Constrained by the phasing decision

The last bullet above — "does the catalogue need to exist in phase one?" — has been **answered elsewhere and is no longer open here.** [How the effort is phased, and what each phase stands up alone](08-how-the-effort-is-phased.md) ruled that **the catalogue is not a phase at all**:

- Phase 3 ships **stack detection and naming** — "your stack needs a React skill" — and is complete with zero catalogue content in existence.
- Where a gap is named, **phase 2's interview helps the developer author their own**. That is the default answer for every uncovered stack, including the internal-framework and COBOL cases this ticket worries about. Those teams are no longer worse off than mainstream ones; they simply get authoring help instead of a ready-made file.
- The catalogue is an **optional content stream** that upgrades phase 3 from "help me write one" to "here, take this one". It can start at any time, or never.

**The hard constraint on this ticket: whatever you decide, it must not become a prerequisite for shipping phase 3.** An answer that makes the toolkit wait on content is out of bounds, however good the content would be.

This lowers the stakes considerably — the map's largest unscoped risk is now off the critical path — but it does not dissolve the ticket. Still to decide: the source, the ownership, the intake bar for anything external, and how an installed skill stays current. The mandatory code-review artefact remains the test case that every option must handle.

## Answer

**The catalogue is taken to zero. Nothing is authored, everything is generated per repo, one artefact at a time.**

All four options in the question are rejected, including the hybrid. The reason the first three could not be chosen was never quality — it was that every one of them is a permanent staffing commitment, and this map has no owner willing to make it.

What replaces the catalogue is not a smaller catalogue. It is a **skeleton plus a per-artefact generator**.

### Why "the model already knows the library" won the argument

The strongest case for a catalogue was that a skill contains two kinds of knowledge and only one is in the repo — **repo knowledge** (where code lives, what the commands are) which generation gets right, and **library knowledge** (that TanStack Query's `staleTime` and `gcTime` are constantly confused, that Apollo cache normalisation fails in specific ways) which no amount of reading `package.json` produces.

That case does not survive: the model has read those docs, those GitHub issues and those blog posts. Library knowledge is in the weights, not in the repo, and it does not need a human-maintained file to reach the team. Authoring a document to tell the model something it already knows is the definition of a wasted commitment.

**Revisit trigger, not a permanent closure:** if generated skills turn out to be observably bad about a specific library, that library — and only that library — earns an authored document. The catalogue reopens one entry at a time, driven by evidence, and never as a programme.

### The code-review test case argues the same way

The ticket named the mandatory code-review artefact as the test that the weakest option fails first. It fails the *catalogue* options. How a team reviews code is close to the definition of house style, and constraint 2 forbids imposing one team's style on another. Generating it per team is not a compromise here — it is the correct answer, and a shared catalogue version would have been actively wrong.

This also settles the question ticket 01 left open: **the mandatory part is having a code-review artefact at all; its content stays the team's own.** Confirmed rather than assumed, as ticket 01 asked.

### Marked stubs

The first run writes a **stub file for every recommended artefact**, at the correct path, each one line long and carrying a `TODO(floor):` marker naming what it is for.

Chosen over a list in the report because the report is easy to ignore and a directory of files is not. A beginner can see the shape of what a configured repo looks like by looking at the file tree, and an unfilled stub is visible in every pull request. That is a real adoption advantage, and adoption has been the weakest point of this effort — the honest answer so far was "nothing makes a team run this".

**Two rules make stubs safe, and both are mandatory:**

1. **A `TODO(floor):` marker means absent.** The rubric treats any artefact carrying the marker as *not present*, so a stub can never produce a false pass. Without this rule the toolkit would manufacture passes with its own hands — ticket 05 established that every file the toolkit leaves behind counts toward the floor.
2. **A stub tells the agent to ignore it.** The body is one line: the `TODO(floor):` question, and an explicit instruction that this file is unfilled and carries no guidance. A stub that is never filled must cost the reading agent nothing beyond that line. Stubs are the one exception to "the toolkit's artefacts are never left behind", and this is the price of the exception.

The report tracks how many stubs remain unfilled, so a repo that stalls at eight empty stubs is told so plainly rather than being counted as progress.

### Per-artefact generators, not one

One generator that writes instructions, skills, sub-agents and a code-review artefact would be shallow at all four. Instead each artefact has its own generator, and the team works through the stubs **one at a time**, each as a separate opt-in run.

This makes the discrepancy-driven interview possible, which is a better questioning rule than the fixed list ticket 11 settled: **ask only where the code is ambiguous.** Two files following different patterns, two different styles of React usage — the generator surfaces the disagreement and asks which one is the standard. A repo that is internally consistent gets almost no questions.

**The reading budget becomes per run, not per repo.** The first run keeps the fixed cheap budget from [How the tool detects the stack](10-how-the-stack-is-detected.md) and produces the stubs. Each artefact run then gets its own budget, **scoped to the directories that artefact is about** — the API layer for a backend skill, the components for a React skill. Nothing is ever unbounded, because the artefact being built is what scopes the read. This also keeps the question cap survivable: six questions per run is fine, thirty in one sitting is the abandonment failure ticket 11 warned about.

### Uncovered stacks

The ticket's worst case — an internal framework, COBOL, something niche — **disappears entirely**. There is no catalogue to be absent from. A COBOL team and a React team get the identical treatment: stubs, then a generator that reads their code and interviews them. The tool no longer fails hardest for teams furthest from the mainstream, because there is no mainstream encoded anywhere in it.

### Ownership, updates, and standardisation

- **Ownership:** nobody owns catalogue content, because there is none. What is owned is the toolkit — the stub list, the rubric `floor.md`, and the per-artefact generators. That is code and prompts, not a growing content library, and it does not expand every time any team adopts any library.
- **How an installed skill stays current:** the question dissolves. A generated skill is the team's own file from the moment it is written — there is no upstream, so there is no fork and no rot relative to anything. It ages with the codebase like any other file, and re-running the generator re-checks it against the code. The corresponding fog item on the map ("How an installed recommended skill stays current") is closed by this.
- **Standardisation:** the risk is gone by construction. Nothing is shared between teams, so nothing can be imposed. Constraint 10's line no longer needs policing, because there is no shared artefact to police.

**Phasing constraint honoured:** this makes nothing a prerequisite for anything. There is no content to wait on.
