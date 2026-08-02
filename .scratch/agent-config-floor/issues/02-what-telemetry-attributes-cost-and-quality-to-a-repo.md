# What telemetry can attribute token spend and quality to a repo

Type: research
Status: open — deferred to the final phase
Blocked by: 08

> **History:** briefly ruled out of scope when measurement was dropped, then reinstated when the destination was redrawn as phased. It is in scope and deliberately last. Do not let it pull work forward — nothing before the final phase may depend on its answer.

## Question

The final phase adds quantitative measurement of token spend and response quality. Establish what is actually observable before that phase is designed.

Investigate and report, with citations to primary vendor documentation:

- **Claude Code.** What usage and cost telemetry is exposed at organisation level — OpenTelemetry metrics, admin/usage APIs, analytics exports. Which fields, at what granularity, and critically: **can spend be attributed to a specific repository**, or only to a user, an API key, or a workspace?
- **GitHub Copilot.** The same for org-level usage metrics APIs and premium-request or spend reporting. Per-repo attribution again being the load-bearing question.
- **Retention and latency.** How far back each goes and how quickly data lands. This decides whether the final phase can look backwards at all, or only forwards from the day it ships — which in turn feeds the data-capture decision in [How the effort is phased, and what each phase stands up alone](08-how-the-effort-is-phased.md).
- **Response quality signals.** What bears on quality rather than cost — session outcomes, PR acceptance or revert rates, review churn on agent-authored PRs, suggestion acceptance rate. Separate what the vendors report from what would have to be derived from our own git and PR history.
- **Consent surface.** What enabling each path requires, who can see the result, and whether a team can opt out while still using the tooling. The invitational constraint means some teams will refuse, and the final phase has to work anyway.

Report what is measurable, what is not, and what would require building our own collection. Flag explicitly anything the vendors do not provide.
