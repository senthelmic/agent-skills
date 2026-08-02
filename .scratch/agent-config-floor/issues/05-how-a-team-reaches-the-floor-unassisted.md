# How a team runs the floor tool with zero platform involvement

Type: grilling
Status: open
Blocked by: 01, 03

## Question

The invitational constraint means a team that wants nothing to do with the platform team must still have a complete, unaided path to the floor. Decide what that path is.

The delivery mechanism is the decision:

- **A plugin** teams install and subscribe to, updating as the floor evolves — but it lands inside their agent config, which is itself the thing being audited.
- **An npm package / CLI** run on demand — familiar, versionable, but a separate install and easily left to rot at an old version.
- **A skill** invoked inside Claude Code or Copilot — zero install for teams already using the agents, but it inherits the agent's non-determinism, and a check that gives different answers on different runs is not an audit.
- **A hosted service or dashboard** — nothing to install, but it needs repo access, which collides with the consent constraint.

Pressure to apply:

- **Which parts must be deterministic and which can be judgement?** The floor check should give the same answer twice; generating tailored config should not be mechanical. These may want different mechanisms, and forcing one mechanism to do both may be the mistake.
- **The adoption paradox.** A voluntary tool is run least by the teams furthest below the floor. What in this path makes a team at zero actually run it — and if the honest answer is "nothing", say so, because that changes the effort.
- **How does the team know the floor moved?** Whatever ships has to carry updates without the platform team pushing them.

Resolution is the delivery mechanism, with the determinism split stated explicitly.
