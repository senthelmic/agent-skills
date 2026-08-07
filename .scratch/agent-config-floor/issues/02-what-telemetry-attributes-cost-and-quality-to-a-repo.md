# What telemetry can attribute token spend and quality to a repo

Type: research
Status: open — deferred to the final phase

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

## Rescoped by the phasing decision

[How the effort is phased, and what each phase stands up alone](08-how-the-effort-is-phased.md) unblocked this ticket and settled two of its inputs in advance:

- **The one dataset that definitely exists is the committed report file.** Phase one writes a dated, version-stamped report with a machine-readable header (verdict per capability) at a stable path, and teams that choose to commit it accumulate a floor history in their own git log. This is not telemetry — nothing leaves the machine — but it is the final phase's only guaranteed source of *before and after* data.
- **Coverage is patchy by design.** The tool never commits the file, so some repos will have years of history and some none. Any design here must work on partial coverage and must not propose making the commit mandatory, which would breach the invitational constraint.

The retention-and-latency bullet above is therefore no longer load-bearing for the capture decision — that is settled. It still matters for judging what the vendors can add on top.
