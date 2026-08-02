# What telemetry can attribute token spend and quality to a repo

Type: research
Status: closed — out of scope

> **Ruled out of scope before any work started.** The destination no longer commits to measuring anything: effectiveness is judged qualitatively with an LLM as the judge, and token spend was named the lowest priority. Telemetry, per-repo attribution and before/after studies all sit past the destination. Kept for the record; do not work it. See the map's **Out of scope** section.

## Question (superseded)

The destination commits to proving the floor changes token consumption and output quality. Before any measurement can be designed, establish what is actually observable.

Investigate and report, with citations to primary vendor documentation:

- **Claude Code.** What usage and cost telemetry is exposed at organisation level — OpenTelemetry metrics, admin/usage APIs, analytics exports. Which fields exist, at what granularity, and critically: **can spend be attributed to a specific repository**, or only to a user, an API key, or a workspace?
- **GitHub Copilot.** The same for org-level usage metrics APIs and any premium-request or spend reporting. Per-repo attribution again being the load-bearing question.
- **Retention and latency.** How far back does each go, and how quickly does data land? This bounds how long a baseline period must run before config changes can be allowed.
- **Quality signals.** What is available that bears on output quality rather than cost — task/session outcomes, PR acceptance or revert rates, code-review churn on agent-authored PRs, suggestion acceptance rate. Distinguish what the vendors report from what would have to be derived from our own git and PR history.
- **Consent surface.** What does enabling each telemetry path require, who can see the result, and can a team opt out while still using the tooling? This bears directly on the invitational constraint — see the map's standing constraints.

Report what is measurable, what is not, and what would require building our own collection. Explicitly flag anything the vendors do **not** provide, since that is what determines whether the measurement commitment survives contact.
