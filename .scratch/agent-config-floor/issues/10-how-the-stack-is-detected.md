# How the tool detects the stack

Type: grilling
Status: resolved

## Question

Recommendations are stack-aware, so the tool must work out what a repo is built from. Decide how.

Decide:

- **Deterministic parsing, or LLM reading?** Manifest files (`package.json`, `pom.xml`, `requirements.txt`, `go.mod`, lockfiles) give an exact dependency list with no judgement and no tokens. An LLM reading the code catches what manifests miss — that the project is full-stack, that GraphQL is used through a wrapper, that a dependency is present but unused. Deterministic is cheap and repeatable; LLM is richer and cannot be trusted to give the same answer twice.
- **Presence versus significance.** `package.json` listing `react` says React is a dependency. It does not say the project is a React project — it might be one component in a mostly-backend repo. A recommendation triggered by a dependency nobody uses is noise, and noise is how a tool stops being run. What threshold makes a dependency worth a skill?
- **Monorepos.** One repo, several stacks. Does the tool report per-package or per-repo? Per-repo recommendations for a monorepo will be wrong for every package in it.
- **Which signals justify which recommendation?** The stated trigger is libraries with a steep learning curve or easily-misused patterns — TanStack Query, Apollo GraphQL, React Hook Form. That is a judgement about the *library*, not about the repo, so it belongs in the catalogue as metadata rather than in the detector. Confirm that split.

Note the dependency: **what** gets recommended depends on [Where the recommended skills come from](09-where-recommended-skills-come-from.md). **Whether the stack can be identified at all** does not, so this ticket can be worked first.

Resolution is the detection method, the significance threshold, and the monorepo answer.

## Answer

**Manifests are the source of truth; the agent reads them, and reads nothing beyond a fixed budget.**

The deterministic-versus-LLM framing collapsed when the CLI was withdrawn ([amendment on ticket 05](05-how-a-team-reaches-the-floor-unassisted.md)). There is no deterministic parser to choose any more — the agent does the reading. But *what* it reads is deterministic, and that is where the repeatability comes from.

**The budget** — the same fixed set the gap-finder uses, so both passes share one read:

- every dependency manifest and lockfile found anywhere in the repo (`package.json`, `pyproject.toml`/`requirements.txt`, `go.mod`, `pom.xml`/`build.gradle`, `*.csproj`, `Gemfile`, `Cargo.toml`)
- the directory tree to depth 2
- the README
- CI workflow files

No source-file reading for stack detection. It does not recurse, it does not grow with the repo, and two runs read the same files.

**Presence versus significance: manifest presence is the trigger, and the threshold is honesty rather than counting.** The agent does not try to measure how much a dependency is used — that needs the deep code reading the budget forbids. Instead a dependency named in a manifest produces a recommendation **marked with its evidence**: "`package.json` lists `@tanstack/react-query`". The developer confirms or dismisses in the interview. Cheap, repeatable, and it moves the significance judgement to the person who knows.

One exception, cheap enough to keep: a dependency listed only under `devDependencies` (or the language's equivalent) is reported as such and ranked lower.

**Monorepos: per-package detection, per-repo floor.** The two halves of the tool answer differently, and conflating them was the trap in this ticket.

- The **floor** is per-repo. One `CLAUDE.md`, one set of instructions, one code-review prompt — agents read from the repo root, so the floor cannot be per-package.
- **Recommendations** are per-package. Each manifest found is one package, named by its directory. A repo with `apps/web` (React) and `services/api` (Go) gets both sets, each labelled with its path, never merged into one wrong list.

Detection of "this is a monorepo" is exactly "more than one manifest found". No workspace-file parsing, no tooling-specific knowledge.

**Library significance is catalogue metadata, not detector logic — confirmed.** The detector answers only "is `@tanstack/react-query` present, and where". Whether that library deserves a skill is a fact about the library, held with the skill in the catalogue. The detector needs no opinion about any library, so it needs no updating when opinions change. That split is what lets [Where the recommended skills come from](09-where-recommended-skills-come-from.md) stay an independent content stream that no phase depends on.

**Greenfield falls out for free.** No manifests found means no stack, which is the honest answer, and the interview goes to greenfield mode. See [How the interview works](11-how-the-interview-works.md).
