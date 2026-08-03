# Where the recommended skills come from

Type: grilling
Status: open

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

Resolution is the source, the ownership, and the answer for uncovered stacks.
