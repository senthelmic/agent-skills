# How the CLI reaches teams without Node

Type: grilling
Status: resolved — closed as moot
Blocked by: 05

## Question

[How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md) settled `npx @org/floor`, always-latest, as the front door. `npx` assumes Node is installed. A Python, Go, Java or .NET repo may have no Node at all, and asking such a team to install a runtime before they can be told what is missing reintroduces the install friction the mechanism exists to remove.

Decide how the CLI reaches those teams:

- **npm only, and accept the exclusion.** Simplest. Means the tool's reach is shaped by an implementation detail rather than by need.
- **A single self-contained binary** (Go, Rust, or a compiled Node bundle) distributed per platform, with `npx` kept as one convenience path among several.
- **A container image** — `docker run`. No runtime to install, but Docker is its own prerequisite and mounting the repo is friction.
- **Multiple distributions of one core** — npm, Homebrew, and a downloadable binary, all wrapping the same logic.

Pressure to apply:

- **Which languages do the target repos actually use?** This is a fact about the organisation, not a preference. If the estate is overwhelmingly Node, the exclusion may cost nothing and the whole question dissolves.
- **Always-latest must survive.** Ticket 05 rejected pinning on purpose. A Homebrew formula or a downloaded binary is a pinned artefact by nature, and a stale binary reporting against last quarter's floor is exactly the failure `npx` was chosen to avoid. Whatever is picked must either self-update or refuse to run when stale.
- **The generate half needs an agent anyway.** A team with no Node still needs Claude Code or Copilot installed for `--setup` to mean anything. Consider whether the audit alone is worth a separate distribution channel.

Resolution is the distribution set, with the staleness answer stated explicitly for any channel that is not always-latest.

## Closed as moot

The premise is gone. [How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md) was amended to withdraw `npx @org/floor` entirely — delivery is now a skill run by the agent the team already has.

There is no Node dependency to reach around. A Python, Go, Java or .NET team installs nothing; the only prerequisite is an agent, which is the thing this toolkit exists to help them adopt.

No decision was made here and none is owed. Closed without an answer.
