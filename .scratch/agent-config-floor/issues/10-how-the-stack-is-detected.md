# How the tool detects the stack

Type: grilling
Status: open

## Question

Recommendations are stack-aware, so the tool must work out what a repo is built from. Decide how.

Decide:

- **Deterministic parsing, or LLM reading?** Manifest files (`package.json`, `pom.xml`, `requirements.txt`, `go.mod`, lockfiles) give an exact dependency list with no judgement and no tokens. An LLM reading the code catches what manifests miss — that the project is full-stack, that GraphQL is used through a wrapper, that a dependency is present but unused. Deterministic is cheap and repeatable; LLM is richer and cannot be trusted to give the same answer twice.
- **Presence versus significance.** `package.json` listing `react` says React is a dependency. It does not say the project is a React project — it might be one component in a mostly-backend repo. A recommendation triggered by a dependency nobody uses is noise, and noise is how a tool stops being run. What threshold makes a dependency worth a skill?
- **Monorepos.** One repo, several stacks. Does the tool report per-package or per-repo? Per-repo recommendations for a monorepo will be wrong for every package in it.
- **Which signals justify which recommendation?** The stated trigger is libraries with a steep learning curve or easily-misused patterns — TanStack Query, Apollo GraphQL, React Hook Form. That is a judgement about the *library*, not about the repo, so it belongs in the catalogue as metadata rather than in the detector. Confirm that split.

Note the dependency: **what** gets recommended depends on [Where the recommended skills come from](09-where-recommended-skills-come-from.md). **Whether the stack can be identified at all** does not, so this ticket can be worked first.

Resolution is the detection method, the significance threshold, and the monorepo answer.
