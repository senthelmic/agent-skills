# How a team runs the floor tool with zero platform involvement

Type: grilling
Status: resolved
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

## Answer

**Two mechanisms, split on determinism, behind one front door.**

| | Audit (goal 1) | Generate (goal 2) |
|---|---|---|
| Mechanism | `npx @org/floor` — a CLI | An ephemeral skill, delivered by the same CLI |
| Nature | Deterministic computation: files present × GitHub's reach matrix | Judgement: interview, stack detection, vendor generator |
| Same answer twice? | Yes, required | No, and that is correct |

`npx @org/floor` audits and prints the report. `npx @org/floor --setup` starts generate. One command for the team to learn, not two products.

Forcing one mechanism to do both was rejected in both directions. A single skill makes the audit inherit the agent's non-determinism, and a check that answers differently on different runs is not an audit. A single CLI means writing our own content generator, which [Build, fork, or wrap the existing audit tools](03-build-fork-or-wrap-existing-tools.md) forbade.

### Always-latest, no pinning

`npx` resolves the current version every run. No pinned devDependency is offered.

The audit is a **report**, not a build step — nothing downstream needs its output to be byte-identical next quarter. The opposite is wanted: last quarter's pass should become this quarter's "you are now below a raised floor". Reproducibility is a virtue for builds and a defect for a moving standard. This also answers the ticket's third pressure point without any push mechanism: the floor's version travels with the tool, so no team is ever told the floor moved — they find out by running it.

**Required consequence: every report is dated and names the floor version it was judged against.** Without that a team cannot tell a stale pass from a current one.

### Generate routes to the vendors' own generators

One agent-agnostic skill. It runs the interview and the stack detection — the parts that are genuinely ours — and at the point prose must be written it directs the developer to their vendor's generator, then reviews and completes the result:

- **Claude Code** — `/init`. Available wherever Claude Code runs.
- **Copilot** — VS Code's Command Palette "Chat: Generate Instructions" / "Chat: Configure Instructions", which generates `.github/copilot-instructions.md` from the codebase. **This is a VS Code feature only** — not in Visual Studio, JetBrains, Xcode, or on github.com.

**Known gap, named rather than filled:** a Copilot-only team not using VS Code has no vendor generator. The report says so plainly. The skill does not write the content itself, because that would break ticket 03's rule. Spun off as [Does Copilot CLI have an instruction-generating command](13-copilot-cli-instruction-generation.md) — if it does, the gap closes and no target is second-class.

The skill's value was never prose generation. It is the interview, the stack detection, and knowing which files must exist.

### The skill is ephemeral

`--setup` writes the skill into the skills directory, tells the developer to invoke it, and removes it when the run finishes.

This is what makes always-latest actually hold. A permanently committed skill is a fork frozen at its install version, silently undoing the previous decision — and it puts our files inside the very config being audited, which is the objection this ticket raised against the plugin option.

**Rule this establishes: every file the toolkit leaves behind is a file the team owns and the floor counts. The audit never counts the toolkit's own artefacts toward the floor.** A repo must not pass because our machinery is sitting in it.

Accepted cost: the skill only exists while the developer is working, so an abandoned setup must be resumed by re-running `npx @org/floor` — the same one command they already know. The report states where they stopped.

Note for [Where the recommended skills come from](09-where-recommended-skills-come-from.md): "ephemeral" governs **our machinery only**. Catalogue skills a team installs are theirs, permanent, and still drift. That fog item is unchanged.

### The adoption paradox: the honest answer is "nothing"

Nothing in `npx @org/floor` makes an uninterested team run it. The ticket asked for this to be said plainly if true, and it is true.

What the mechanism can do is remove every reason not to, and this one does: no install, no account, no repo access to grant, no consent conversation, one command, and useful output on the first run even in a repo with zero config. That is the whole of the delivery mechanism's contribution to adoption.

The rest is not a mechanism problem and already has two homes in the map's fog — **the shape of the assisted offer** and **rollout and pilot selection**. Solving it inside the delivery mechanism is precisely where an invitational tool becomes an enforced one and breaks constraint 1.

**One lever claimed now, because it is free and breaches nothing: seed the org's new-repo template so a greenfield repo starts at the floor.** It does nothing for existing repos, but it stops the below-the-floor population from growing, and every developer meets the tool at least once.

### Never phones home

Zero network calls beyond the registry fetch of the tool itself. No telemetry, no opt-in ping, no first-run consent prompt.

This is not a preference being expressed — the map's ubiquitous language already defines **self-serve** as "zero platform-team involvement and **no visibility to us**". Telemetry here would contradict settled language, and re-opening it would be relitigating charting.

The report is **written to a file**. That is not measurement; it is the artefact a team pastes into a channel when they decide they want the assisted path, which makes it the natural bridge between self-serve and assisted.

**Consequence recorded honestly: the final phase cannot backfill history.** Whatever it measures, it measures forward from the day it ships. [What telemetry can attribute token spend and quality to a repo](02-what-telemetry-attributes-cost-and-quality-to-a-repo.md) and [What the LLM judge assesses, and against what rubric](06-how-the-floors-effect-gets-measured.md) inherit that.

### What this ticket did NOT settle

- **How the CLI reaches teams without Node.** `npx` assumes Node is available, which a Python, Go, Java or .NET repo may not have. Spun off as [How the CLI reaches teams without Node](14-how-the-cli-reaches-non-node-teams.md).
- **What the report says.** Its shape and tone belong to [What the floor report looks like to a team below it](07-what-the-floor-report-looks-like.md). This ticket only fixes that it is dated, version-stamped, written to a file, and useful on a zero-config repo.

## Amendment: the CLI is withdrawn, delivery is agent-native

Superseded on the user's instruction while resolving [What the LLM judge assesses, and against what rubric](06-how-the-floors-effect-gets-measured.md). **`npx @org/floor` is gone.** So is the determinism split that justified it.

**What replaces it.** One **skill**, run by the agent the team already has. No Node, no runtime, no install step of its own.

The reason the CLI existed was to keep the audit deterministic. That reason is withdrawn: near-determinism is now bought inside the agent — fixed reading budget, lookup-shaped checks, fixed output schema, strong system prompt — and the residual variation is disclosed rather than engineered away. See ticket 06's reproducibility section.

**What this fixes.** [How the CLI reaches teams without Node](14-how-the-cli-reaches-non-node-teams.md) is now moot and closed. A Python, Go, Java or .NET team installs nothing. The prerequisite is exactly the thing the team is being helped to adopt — an agent — which is the correct prerequisite for a tool whose whole purpose is agent enablement.

**Install: the agent fetches its own instructions.** The team pastes one line into Claude Code or Copilot:

> Fetch `<published URL>` and follow it.

The agent retrieves the skill and runs it. Fallback for an agent with no network access, or an air-gapped repo: `git clone --depth 1 <repo>` and point the agent at the local path. Both routes are one step and neither needs a package manager.

**What survives unchanged from the original answer:**

- **Always-latest, no pinning.** The fetch resolves the current version every run, so the floor's version travels with the tool and nobody is told the floor moved.
- **Every report is dated and names the floor version it was judged against.**
- **Never phones home.** The report is a local file; sharing it is the voluntary bridge into the assisted path.
- **Ephemeral machinery.** Nothing the toolkit needs for itself is committed, and the toolkit's own artefacts never count toward the floor. This is now easier to honour, since a fetched skill leaves nothing behind by default.
- **The adoption answer is still "nothing"**, and the new-repo-template lever still stands.

**What changes in the generate half.** Routing to the vendors' own generators (`/init`, VS Code's "Chat: Generate Instructions") is **kept as the preferred path** where one exists, because the vendor's generator is better at reading a codebase than our prompt is. But the prohibition on ever writing content ourselves is **relaxed to a fallback**: where no vendor generator reaches the team's surface, the skill generates the artefact itself rather than reporting a hole. Ticket 03's rule was "do not build a generator to compete with the vendors'", and a fallback for surfaces the vendors do not reach does not compete with anything. This also removes [Does Copilot CLI have an instruction-generating command](13-copilot-cli-instruction-generation.md) from the critical path — useful if the answer is yes, no longer blocking if it is no.
