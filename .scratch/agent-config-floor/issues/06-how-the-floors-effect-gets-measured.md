# What the LLM judge assesses, and against what rubric

Type: grilling
Status: open
Blocked by: 01

> Reshaped when measurement was deferred to the final phase. Previously "How the floor's effect gets measured" — a quantitative before/after study, now handled by [What telemetry can attribute token spend and quality to a repo](02-what-telemetry-attributes-cost-and-quality-to-a-repo.md) much later. This ticket is the **qualitative** judging that every earlier phase relies on, and it must work with no telemetry whatsoever. A judge with no rubric is just a vibe.

## Question

Effectiveness is judged by an LLM rather than measured. Decide what the judge is actually given, what it returns, and what makes its verdict trustworthy enough to act on.

Decide:

- **What the judge reads.** The config files alone? The config plus the repo it describes — so it can catch instructions that are accurate-sounding but wrong about the codebase? Reading the repo is far more useful and far more expensive; pick, and say why.
- **What it returns.** A pass/fail against the floor, a per-item verdict, a prose critique, or a prioritised list of fixes. This has to survive being handed to a team that did not ask for it — see [What the floor report looks like to a team below it](07-what-the-floor-report-looks-like.md).
- **The rubric.** The judge needs criteria, and the honest source is the floor itself. What does "good" mean for each floor item beyond existing — accurate, current, specific to this repo, not contradicting the code? Write the criteria the judge is scored against.
- **Reproducibility.** Two runs on an unchanged repo should not disagree. Decide the tolerance: is the pass/fail deterministic with only the prose varying, or is disagreement acceptable? A judge that flips verdicts cannot support the assisted engagement.
- **Which agent judges.** Claude Code, Copilot, or either. If the tool is delivered as a skill, the judge is whatever the team runs, and verdicts will differ between them.
- **Self-grading.** If the same tool both generates config and judges it, it will mark its own homework. Decide whether that matters here, and if so, what breaks the loop.

Resolution is the judging design: inputs, output shape, rubric, and the stated limits of what a judged verdict is worth.
